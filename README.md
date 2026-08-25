# PCD

> University coursework project (PCD — Concurrent and Distributed Programming)
> Author: **Juan Jiménez Serrano**

This repository collects a series of Java concurrency exercises for the **PCD** course, each one on its own branch, progressing from a plain (non-thread-safe) data structure to classic synchronization problems solved with monitors and, later, explicit locks/conditions. `master` mirrors the latest branch (`PCD_P7`).

## Branches overview

| Branch | Problem | Concurrency mechanism | UI |
|---|---|---|---|
| `PCD_P1` | Bounded stack (`Pila`), single-threaded | None — sequential test only | Console |
| `PCD_P2` | Producer/consumer pushing and popping from a stack (`PilaLenta`) | **None (intentional)** — demonstrates race conditions on an unsynchronized shared stack | Console |
| `PCD_P3` | Same producer/consumer stack | `synchronized` methods (mutual exclusion) | Swing/AWT `Canvas` visualizing the stack state |
| `PCD_P4` | Same producer/consumer stack, now a proper bounded buffer | `synchronized` + `wait()`/`notifyAll()` — producer blocks when full, consumer blocks when empty | Swing/AWT `Canvas` |
| `PCD_P5` | New problem: a rehabilitation center (`Centro`) shared by masseur, rehab therapist and a single changing room, with waiting queues | `synchronized` + `wait()`/`notifyAll()` | Swing `Canvas` showing queues and occupancy |
| `PCD_P7` (= `master`) | New problem: a shared feeder (`Comedero`) for cats and dogs, with a total capacity and a species-ratio constraint | `java.util.concurrent.locks.ReentrantLock` + `Condition` (one condition per species) | Swing `Canvas` showing both queues and both feeder groups |


## Branch details

### `PCD_P1` — Basic stack
`Pila` (implementing `iPila`) is a simple array-backed stack with `Apila`/`Desapila`/`Primero`/`GetNum`, exercised from `UsaPila.main` with no threads involved.

### `PCD_P2` — Producer/consumer, no synchronization
`PilaLenta` is the same stack with an artificial `Thread.sleep(100)` added to `Apila`/`Desapila` to widen the race-condition window. `Productor` (extends `Thread`) and `Consumidor` (implements `Runnable`) push/pop concurrently with **no locking at all**, so `UsaPila.main` (which starts 2 producers + 2 consumers) is expected to show inconsistent/incorrect results — this is the point of the exercise.

### `PCD_P3` — Mutual exclusion + visualization
`Apila`/`Desapila` become `synchronized`, removing the race condition (though not yet solving blocking on a full/empty stack). `PilaFrame` (a NetBeans Swing form) and `CanvasPila` render the stack's contents live as items are pushed/popped.

### `PCD_P4` — Bounded-buffer with wait/notify
Builds on P3: `Apila` calls `wait()` while the stack is full, `Desapila` calls `wait()` while it's empty, and both call `notifyAll()` after modifying the stack — the textbook monitor-based solution to the bounded producer/consumer problem.

### `PCD_P5` — Rehabilitation center
A different, more elaborate synchronization problem: `Centro` manages a masseur, a rehab therapist and a single changing room, each usable by only one client at a time, with `ArrayList`-based waiting queues for the massage and rehab stations. `Masaje` (a `Thread`) and `Rehabilita` (a `Runnable`) simulate clients moving through `EntraMasaje`/`SaleMasaje`/`EntraRehabilitacion`/`SaleRehabilitacion`/`Termina`, synchronized with `synchronized` + `wait()`/`notifyAll()`. `GeneradorFrame` + `CanvasCentro` visualize occupancy and queues.

### `PCD_P7` — Shared feeder (cats & dogs)
`Comedero` models a feeder with a total capacity of 4 spots, where dogs and cats compete under specific ratio constraints (e.g. cats can't enter if there are already 3 dogs, or 2 cats + 1 dog). Unlike the earlier branches, this one uses explicit `java.util.concurrent.locks.ReentrantLock` with **two separate `Condition`s** (one per species) instead of the built-in `synchronized`/`wait`/`notify`. `Gato` and `Perro` (both `Runnable`) simulate animals entering/leaving; `Generador` + `CanvasComedero` visualize both queues and both groups currently feeding.

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java |
| Concurrency | `java.lang.Thread`, `Runnable`, `synchronized`/`wait`/`notifyAll` (P3–P5), `java.util.concurrent.locks.ReentrantLock`/`Condition` (P7) |
| UI (P3–P7) | Java AWT/Swing (`Canvas`, NetBeans `.form` GUI files) |
| Build tool | Apache Ant (NetBeans-generated `build.xml`) |
| IDE | Apache NetBeans |

## Prerequisites

- **JDK** (any reasonably modern version; the projects use NetBeans' default Ant build, no explicit modern-Java features)
- **Apache NetBeans** (recommended — each branch is a NetBeans Ant-based Java project; some also include Swing `.form` GUI files best edited in NetBeans)
- Apache Ant, if you prefer building from the command line instead of NetBeans

## Installation Steps

1. **Clone the repository:**
   ```bash
   git clone https://github.com/jujise96/PCD.git
   cd PCD
   ```

2. **Check out the branch (exercise) you want to run:**
   ```bash
   git checkout PCD_P4   # or PCD_P1, PCD_P2, PCD_P3, PCD_P5, PCD_P7
   ```

3. **Build and run:**
   - From NetBeans: open the checked-out project folder and press *Run*.
   - From the command line, with Ant:
     ```bash
     ant run
     ```
     (For `PCD_P5` and `PCD_P7`, the actual NetBeans project lives in a nested subfolder — `Practica5_PCD/` and `Practica7_PCD/` respectively — so `cd` into that folder first.)

## Author

**Juan Jiménez Serrano**.
