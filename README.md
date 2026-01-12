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

**Netlify / Build fixes applied**
- **Added Node types**: `@types/node` was added to `devDependencies` so TypeScript can resolve Node builtins during the build.
- **tsconfig updates**: `moduleResolution` changed to `node` and `types` now include `node` and `vite/client`. `tsconfig.node.json` also includes `types: ["node"]` for `vite.config.ts` compilation.
- **vite.config.ts (ESM-safe)**: Replaced `node:path` and `__dirname` usage with `import path from 'path'` + `fileURLToPath(import.meta.url)` to compute `__dirname` in ESM.
- **TaskForm createdAt**: `createdAt` is now provided for newly created tasks (and preserved on edit) so the `Task` type requirement is satisfied during `tsc` builds.

**Files changed for build fixes**
- [package.json](package.json): added `@types/node` to `devDependencies`.
- [tsconfig.json](tsconfig.json): switched `moduleResolution` to `node` and added `types`.
- [tsconfig.node.json](tsconfig.node.json): added `types: ["node"]` and set `moduleResolution` to `node` for vite config compilation.
- [vite.config.ts](vite.config.ts): use ESM-safe `__dirname` pattern (`fileURLToPath(import.meta.url)`) and import `path` normally.
- [src/components/TaskForm.tsx](src/components/TaskForm.tsx): include `createdAt` when creating new tasks.






