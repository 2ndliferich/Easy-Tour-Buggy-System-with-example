# Tour Buggy System (Horizon Worlds)

A simple, reliable system for guided tours using autonomous buggies.
Players hop on, tap **Start Buggy**, and the vehicle follows your path logic using **Guidance Triggers** (turns, straightaways, stops).

**Files**

* `TourBuggy.ts` — movement + state (start/stop, reset, facing)
* `TourBuggyUI.ts` — in-world UI (Start/Stop) with target binding
* `tourBuggyGuidance.ts` — trigger logic (stop / travel axis / face axis)

**Includes**

* 1-seater, 2-seater, and 4-seater buggies
* Each buggy has an **Avatar Pose Gizmo** and a **Custom UI** gizmo nested so they move together
* A **Guidance Trigger** prefab (duplicate as needed)
* A **Stop Trigger** variant (Guidance Trigger with `stop` toggled on)

---

## How it works

1. **Player boards a buggy** and taps **Start Buggy** on the UI.
2. The buggy moves straight ahead until it **enters a Guidance Trigger**.
3. A **Guidance Trigger**:

   * Can **Stop** the buggy (and reset it to its start position), or
   * Send new **travel** / **face** directions (world-space axes).
4. Without a trigger in its path, the buggy keeps going—so place triggers to define turns, straight segments, and the endpoint.

> When a buggy is stopped, it **returns to its starting position** and the **UI resets**.

---

## Quick Start

1. **Pull the Tour Buggy System** into your scene.
2. In the hierarchy, **right-click → Unparent Child Objects**, then **delete** the outer container entity.
3. Each buggy already includes the **Avatar Pose** and **UI** gizmos as children.
4. **Place Guidance Triggers** on your route:

   * Start by sliding a trigger into the buggy’s path to line it up, then move it outward and **scale it larger** to ensure it catches the buggy reliably.
   * Duplicate triggers to build your path.
5. **(Optional)** Place a **Stop Trigger** at the end of the route (or anywhere you need a reset).
6. **Test**: Sit on a buggy → press **Start Buggy** → verify the buggy follows triggers and stops/returns at the end.

---

## Components & Props

### `TourBuggy.ts` (attach to the buggy root)

| Prop         | Type   | Default | Description                                                                                |
| ------------ | ------ | ------- | ------------------------------------------------------------------------------------------ |
| `moveEntity` | Entity | (self)  | Optional override: which entity to move (useful if the script sits on a parent container). |
| `speed`      | Number | `3`     | Movement speed (units/sec).                                                                |

**Behavior**

* Records initial **position/rotation** the first time it starts, and restores them on **Stop**.
* Listens for **Guidance Triggers** to set the travel direction (and optional facing).
* Emits/receives local events for UI sync.

---

### `TourBuggyUI.ts` (nested in each buggy)

| Prop           | Type    | Default       | Description                                                                                                          |
| -------------- | ------- | ------------- | -------------------------------------------------------------------------------------------------------------------- |
| `startRunning` | Boolean | `false`       | If `true`, start in the “running” state.                                                                             |
| `startLabel`   | String  | `Start Buggy` | Start button label.                                                                                                  |
| `stopLabel`    | String  | `Stop Buggy`  | Stop button label.                                                                                                   |
| `buggy`        | Entity  | (unset)       | Target buggy. If set, UI sends start/stop to that buggy only; if unset, broadcasts (useful for single-buggy scenes). |

**Usage**

* Players tap the UI to **Start** or **Stop**.
* UI auto-syncs its toggle by listening to buggy **state** events.

---

### `tourBuggyGuidance.ts` (attach to a Trigger Gizmo)

| Prop         | Type    | Default | Description                                                                                        |
| ------------ | ------- | ------- | -------------------------------------------------------------------------------------------------- |
| `stop`       | Boolean | `false` | If `true`, stop the buggy, return to start, and reset UI. (`travelAxis` / `faceAxis` are ignored.) |
| `travelAxis` | String  | `''`    | **World-space** direction to move along. Examples: `'z'`, `'-x'`, `'0,1,0'`.                       |
| `faceAxis`   | String  | `''`    | **World-space** direction to face. If empty, the buggy faces `travelAxis`.                         |

**Notes**

* Works with both **OnEntityEnterTrigger** and **OnPlayerEnterTrigger** modes on the trigger gizmo.
* Make triggers **wide/tall** enough to guarantee the buggy intersects them.

---

## Path Design Tips

* **Align first, then expand**: Bump a trigger into the path to align it perfectly, then move and **scale it up** for tolerance.
* **Straightaways**: Place a single trigger with `travelAxis: 'z'` (or your chosen world axis). Leave `faceAxis` empty to auto-face the same way.
* **Turns**: Use consecutive triggers (e.g., `travelAxis: 'z'` → then `'-x'`) to define clear direction changes.
* **Stops & Reset**: Put a **Stop Trigger** at the end to bring the buggy home and clear its UI.

---

## Troubleshooting

* **Buggy doesn’t turn at a trigger**
  Ensure the trigger is **in the buggy’s path**, scaled large enough, and has a valid **`travelAxis`** (e.g., `'z'`, `'-x'`, or a vector like `'0,1,0'`).
* **Start button doesn’t do anything**
  If you have multiple buggies, set each UI’s `buggy` prop to its **specific buggy** so the event is targeted.
* **Stops but doesn’t return to start**
  Verify you started the buggy at the correct spawn point; it resets to the transform captured on the first **Start**.
* **Multiple buggies running**
  Use one UI **per buggy** (with `buggy` bound) to avoid cross-control.

---

## Changelog

* **1.0.0** — Three buggies (1/2/4-seat), start/stop UI, guidance/stop triggers, reset-to-start behavior, world-axis pathing.

---

## License & Credit

* **License:** MIT
* **Credit:** 2ndLife Rich (HumAi LLC)
