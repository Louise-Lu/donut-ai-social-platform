# Frontend Render A/B Benchmark (Memoization Optimization)

## Goal
Verify the claim: page responsiveness improved by around 20% after introducing `useMemo` / `useCallback` / derived-calculation caching.

## Scope
- `src/pages/PostAnalyticsPage.jsx`
- `src/pages/CourseFeedPage.jsx`
- `src/pages/StudentAnalysisPage.jsx`
- `src/pages/components/PostCard.jsx`

## A/B setup
- A (baseline): remove or disable the new memoization/callback optimization locally.
- B (optimized): current optimized branch.
- For each scenario, run 20 times and collect P50 and P95.

## Environments
- Browser: Chrome (same version)
- Device: same machine, power mode fixed
- Network: fixed profile (preferably local backend or stable throttle)
- Build mode: production build (`npm run build && npm run preview`) to avoid dev-only overhead

## Metrics
1. React Profiler commit duration (ms)
- Measure key interaction commits only.
- Typical interactions:
  - Course feed keyword filtering
  - Post analytics metric switching
  - Student analytics chart/page switching

2. Input-to-visible-update latency (ms)
- Start: user input event timestamp (typing/clicking)
- End: visible UI update is painted
- Source:
  - Chrome Performance trace event spans
  - Optional app-side telemetry from `performance.now()` markers

## How to capture
### React Profiler
1. Open React DevTools Profiler.
2. Start profiling.
3. Perform one target interaction.
4. Stop profiling and record commit duration for the corresponding commit.
5. Repeat 20 times per interaction per A/B branch.

### Chrome Performance
1. Open DevTools Performance panel.
2. Start recording.
3. Trigger one target interaction.
4. Stop recording.
5. Record input event timestamp and first visible update timestamp.
6. Repeat 20 times per interaction per A/B branch.

## Result table template
Use one table per interaction.

| Interaction | Variant | Samples | Commit Duration P50 (ms) | Commit Duration P95 (ms) | Input->Visible P50 (ms) | Input->Visible P95 (ms) |
| --- | --- | --- | ---: | ---: | ---: | ---: |
| CourseFeed keyword filter | A baseline | 20 |  |  |  |  |
| CourseFeed keyword filter | B optimized | 20 |  |  |  |  |
| PostAnalytics metric switch | A baseline | 20 |  |  |  |  |
| PostAnalytics metric switch | B optimized | 20 |  |  |  |  |
| StudentAnalysis open analytics | A baseline | 20 |  |  |  |  |
| StudentAnalysis open analytics | B optimized | 20 |  |  |  |  |

## Improvement calculation
For each metric, compute improvement percentage based on P50 and P95:

`improvement = (A - B) / A * 100%`

Example:
- A P50 = 50 ms
- B P50 = 40 ms
- Improvement = (50 - 40) / 50 = 20%

## Claim rule
You can state "around 20% improvement" only when:
- At least one core interaction shows >= 20% improvement in both P50 and P95 for one primary metric.
- No major regression (>10%) appears in other key interactions.
- Profiling screenshots and raw logs are attached (Profiler + Performance traces).

## Evidence checklist
- [ ] React Profiler screenshots for A/B (same interaction)
- [ ] Chrome Performance screenshots for A/B (same interaction)
- [ ] Raw measurement sheet (20 samples each)
- [ ] Calculated P50/P95 and improvement formula
- [ ] Notes for environment and run conditions
