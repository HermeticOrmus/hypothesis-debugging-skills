# Examples

Three worked debugging walkthroughs, each running the full six-phase protocol. The point of each one is the same: the ranked hypothesis table comes before the fix, and the fix addresses the confirmed cause, not the symptom.

---

## 1. A failing unit test

**Symptom**: `npm test` fails on `OrderService.test.ts` with `Cannot read properties of undefined (reading 'id')` at line 47.

### Phase 1: Reproduce

Consistent, not intermittent. `npm test -- OrderService` fails every run. The failing assertion is line 47, `expect(orders[0].id).toBe('order-1')`. So `orders[0]` is `undefined`, which means `orders` is empty or shorter than expected.

### Phase 2: Isolate

```bash
git log --oneline -10
```

The last commit touched the test's mock setup. Minimal case: log `orders` right before line 47. It is `[]`, an empty array. So the service returned no orders where the fixture expected one.

### Phase 3: Hypothesize

| # | Hypothesis | Likelihood | How to test |
|---|-----------|------------|-------------|
| 1 | The repository mock is wired to the wrong instance, so the real (empty) repo is hit | High | Log the mock call count; if zero, the spy is on the wrong object |
| 2 | The fixture shape changed and the filter now excludes every row | Medium | Inspect the fixture and the filter predicate |
| 3 | The service has an async bug and returns before the data resolves | Low | Add an await/timing log around the fetch |

### Phase 4: Test

Hypothesis 1: the mock's call count is zero. The spy was attached to a freshly constructed `OrderRepository` while the service was injected with a different instance from the test container. Confirmed. Hypotheses 2 and 3 never need testing.

### Phase 5: Fix

Root cause: the spy and the injected dependency were two different objects. Minimal fix: attach the spy to the same instance the service receives from the container, rather than constructing a new one. Verify by rerunning `npm test -- OrderService` (now green). Regression guard: the test now asserts the mock was actually called, so a future re-wiring that detaches the spy fails loudly instead of returning an empty array.

**Workaround that would have been wrong**: changing line 47 to `expect(orders[0]?.id).toBe('order-1')` or seeding the real repository. Both make the symptom disappear while leaving the mock disconnected, so the test no longer tests what it claims to.

---

## 2. A flaky CI run

**Symptom**: integration tests pass locally but fail roughly 1 in 5 CI runs. The failure is always on `payment.spec.ts`, test `refunds settle within 10s`, error `timeout`.

### Phase 1: Reproduce

The "intermittent, only in CI, always the same test" shape is the whole clue. Intermittent points at timing, state, or environment, not pure logic. Reproduce locally by running the test in a loop with CI-like concurrency: `for i in $(seq 1 20); do npm run test:int -- payment.spec.ts; done`. It fails about 1 in 6 locally too, which rules out a CI-only network theory.

### Phase 2: Isolate

The test issues a refund, then asserts the refund row is settled. The settlement happens when an inbound webhook from the payment provider is processed. Minimal case: log the timestamp of the assertion and the timestamp of the webhook handler. On failing runs, the assertion fires before the webhook handler completes.

### Phase 3: Hypothesize

| # | Hypothesis | Likelihood | How to test |
|---|-----------|------------|-------------|
| 1 | The test races the webhook: it asserts before the server has processed the inbound settlement | High | Add timing logs; on failures the assert precedes the handler |
| 2 | The 10s timeout is too tight for a loaded CI box | Medium | Raise the timeout and see if failures vanish |
| 3 | The webhook is occasionally dropped, not just late | Low | Count webhook deliveries vs assertions across 50 runs |

### Phase 4: Test

Hypothesis 1: timing logs show that on every failing run the assertion timestamp is earlier than the handler completion timestamp, and the handler does complete a few hundred ms later. The test asserts on a fixed sleep rather than waiting for the settlement event. Confirmed.

### Phase 5: Fix

Root cause: the test polls once after a fixed delay instead of waiting for the settlement to actually occur. Minimal fix: replace the fixed sleep with a poll-until-settled-or-timeout against the refund status. Verify with the 20-run loop (now 20/20 green). Regression guard: the new helper has its own test proving it waits for the condition rather than a wall-clock delay.

**Workaround that would have been wrong** (Hypothesis 2): bumping the timeout from 10s to 30s. That reduces the failure rate without removing the race; under heavier load the race returns, and the slower test masks a genuine latency regression if settlement ever truly slows down.

---

## 3. A production 500

**Symptom**: `POST /api/uploads` returns HTTP 500 for some users. Error tracker shows `S3 PutObject: PermanentRedirect (301)` with `Location` pointing at a `us-east-1` URL. The bucket is in `us-west-2`.

### Phase 1: Reproduce

Not every upload fails, so first find the pattern. Repro: upload a 6.2 MB file via `POST /api/uploads`. It 500s. A 2 MB file succeeds. The boundary is around 5 MB, which is the S3 multipart-upload threshold. Reproducible on demand by file size.

### Phase 2: Isolate

```bash
git log --oneline -10 -- src/storage/
```

No recent change to the storage layer, so this is likely a latent bug exposed by file sizes that only recently appeared in production. The 301 redirect to `us-east-1` for a `us-west-2` bucket means a request is being signed or sent against the wrong region. Small files use single `PutObject`; large files use the multipart path. The boundary lining up with 5 MB isolates the bug to the multipart code path.

### Phase 3: Hypothesize

| # | Hypothesis | Likelihood | How to test |
|---|-----------|------------|-------------|
| 1 | The multipart uploader constructs its own S3 client without the region config that the single-put client has | High | Diff the two client constructions; check the multipart client's region |
| 2 | The bucket region env var is unset in prod, so the SDK defaults to us-east-1 | Medium | Print the resolved region in the prod config |
| 3 | A load balancer or proxy rewrites the host for large bodies | Low | Inspect the outbound request host before it leaves the app |

### Phase 4: Test

Hypothesis 1: diffing the two code paths shows the single-put path reads the shared, region-configured client, while the multipart helper does `new S3Client({})` with no region, falling back to the SDK default of `us-east-1`. Confirmed. Hypothesis 2 is refuted because the env var is set and the single-put path reads it correctly.

### Phase 5: Fix

Root cause: the multipart uploader instantiates its own region-less S3 client instead of using the shared configured one. Minimal fix: pass the shared client (or the region config) into the multipart helper. Verify: the 6.2 MB upload now succeeds against `us-west-2`. Regression guard: a test that asserts the multipart client resolves to the same region as the single-put client, so a future second client construction cannot silently default to `us-east-1` again.

**Workaround that would have been wrong**: catching the 301 and retrying against the region in the `Location` header. That makes uploads succeed while leaving two divergent client configurations in the code; the next region-sensitive operation on the multipart client breaks the same way, and the retry adds a round trip to every large upload.

---

## A note on cost

Every phase above costs time up front. Reproducing on demand is slower than reading the stack trace and guessing. Writing a hypothesis table is slower than editing the first suspect line. Confirming the cause is slower than shipping the patch that turns the build green.

The cost compounds the other way too. The workaround that hid the disconnected mock means the test no longer guards anything. The raised CI timeout means a real latency regression ships unnoticed. The retry-on-redirect means every large upload pays an extra round trip and the second client still has the wrong region. The up-front cost of the protocol is the cheaper of the two.

## Further reading

- [`vibe-engineer-skills`](https://github.com/HermeticOrmus/vibe-engineer-skills): the principle this protocol enacts, hypothesis before help, plus the rest of the Vibe Engineer discipline.
- [`andrej-karpathy-skills`](https://github.com/HermeticOrmus/andrej-karpathy-skills): how the AI should behave while you run the loop, simplicity first, surgical changes, goal-driven execution.
