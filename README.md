# TaskGlitch — Fix Summary

This README documents recent bug fixes applied to the project and includes quick test steps.

**What I fixed**
- **Double Fetch (mount)**: Removed a second opportunistic fetch and added a guard to prevent double-loading during React StrictMode remounts.
- **Undo Snackbar state**: Ensure the `lastDeleted` state is cleared when the snackbar closes so undo only works while the snackbar is visible.
- **ROI calculation & validation**: `computeROI` now validates inputs, avoids division-by-zero, returns `null` for invalid values, and rounds results to 2 decimals.
- **Stable sorting**: Replaced a nondeterministic tie-breaker with a deterministic comparator (title → createdAt → id) to prevent flicker.
- **Dialog double-open**: Stopped event propagation on action buttons so Edit/Delete buttons don't also trigger the row-view dialog.
- **Formatting & safety**: Render notes as plain text (removed dangerous HTML rendering) and display ROI / revenue-per-hour with consistent 2-decimal formatting.

**Changed files (high level)**
- [src/utils/logic.ts](src/utils/logic.ts): ROI validation/rounding and deterministic sorting.
- [src/hooks/useTasks.ts](src/hooks/useTasks.ts): Removed duplicate fetch; added `isDeleted` and `clearLastDeleted`; ensure undo clears flags.
- [src/context/TasksContext.tsx](src/context/TasksContext.tsx): Exposed `isDeleted` and `clearLastDeleted` in the context type.
- [src/App.tsx](src/App.tsx): Wired snackbar `onClose` to call `clearLastDeleted`.
- [src/components/TaskTable.tsx](src/components/TaskTable.tsx): Stop `event` propagation on Edit/Delete buttons; render notes as plain text; ROI formatting.
- [src/components/MetricsBar.tsx](src/components/MetricsBar.tsx): 2-decimal formatting for metrics.

**How to verify (manual)**
1. Start the dev server (example):

```bash
npm run dev
```

2. Open the app in a browser and watch the dev console while refreshing:
- Confirm the tasks fetch runs exactly once on page load (no duplicate network calls).

3. Delete a task and observe the Snackbar:
- Click `Delete` on a task — the snackbar should appear and `Undo` should restore that task.
- If you close the snackbar (auto-hide or manual close), clicking `Undo` afterwards should do nothing.

4. Check sorting stability:
- Create or modify tasks so two items share the same ROI and priority. They should remain in the same order across re-renders.

5. Check ROI display and edge cases:
- Tasks with `timeTaken` = 0 or invalid revenue show `N/A` for ROI (no Infinity/NaN).
- ROI and Revenue/Hour should display with two decimals.

If you want, I can run the dev server in the terminal and show logs or run a TypeScript build to confirm there are no type errors.

---
If you'd like this committed to a branch or pushed, tell me the preferred commit message and remote branch.
