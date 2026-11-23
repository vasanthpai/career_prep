# React — revision notes

Tracker: [React Roadmap](https://claude.ai/artifact/6YhmsekhASdtapD9fQThAG)

## 1.1 Rendering & state snapshots (2026-11-23)

**Core rule:** state is a snapshot. A setter doesn't change the variable; it queues an update and asks React for a new render.

- A render = React calls your component function. Each render has its own props, state and handlers, frozen.
- Updates from one handler are **batched** (React 18+: everywhere, including timeouts and promises), then one re-render.
- Re-render triggers: own state change, parent re-render, context change. `useEffect` alone does not re-render.
- `setX(x + 1)` = snapshot + 1. `setX(c => c + 1)` = latest queued value + 1.
- Queue rule: **a plain value replaces the queue; an updater builds on it.**
  - `setN(n+5); setN(c=>c+1)` → 6 · `setN(c=>c+1); setN(n+5)` → 5 · `setN(n+1); setN(n+1); setN(c=>c+10); setN(n+2)` → 2
- Use the updater whenever the next state depends on the previous one. Updaters must be pure (Strict Mode calls them twice).
- **Stale closure:** a callback (timeout, interval, effect) sees only the render it was created in.
  - Updater fixes stale *writes*. Stale *reads* need `useRef` (1.7).
- Never mutate state: `items.push(x); setItems(items)` → same reference, `Object.is` equal, React skips the render. Use `setItems(prev => [...prev, x])`.

**Interview line:** "A plain value is computed from the current render's snapshot, so several updates in one handler or async callback can overwrite each other. The updater form receives the latest queued state, so updates chain correctly. I use it whenever the next state depends on the previous one."

**My mistakes**
- Tuned numbers (`c => c + 3`, `c => c + 2`) until output looked right instead of tracing the queue.
- Mixed up reading state (log) with updating it.
- Wrote a comment that said the opposite of the behaviour ("prints current count").
- Answered only half of a two-part question.
