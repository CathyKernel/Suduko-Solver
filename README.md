# Sudoku Solver — Backtracking · MRV Heuristic · Bitmask Constraints

A fully client-side Sudoku solver with an interactive grid, real‑time conflict detection, step‑by‑step replay of the search process, and uniqueness verification — all running in your browser.

**Live Demo:** [https://cathyli-suduko-solver.netlify.app/](https://cathyli-suduko-solver.netlify.app/) 

---

## Features

- **Interactive 9×9 Sudoku grid** – click any cell and type `1–9` to enter clues, or use the on‑screen number pad.
- **Pencil‑mark notes** – toggle `Notes` mode (or press `N`) to add/remove candidate digits in a cell.
- **Real‑time conflict detection** – duplicates in rows, columns, or boxes are highlighted in red as you type.
- **Smart solving engine** – combines **backtracking DFS** with the **MRV (Minimum Remaining Values)** heuristic and **bitmask constraint propagation**.
- **Step‑by‑step replay** – watch the algorithm solve the puzzle, with green flashes for placements and red flashes for backtracks.
- **Uniqueness check** – the solver continues after finding the first solution to determine whether a puzzle has a unique completion.
- **Sample puzzles** – one‑click load of classic “easy”, “hard”, and “expert” puzzles.
- **100% client‑side** – no data is sent to any server; all computation is done in your browser.
- **Keyboard shortcuts** – full keyboard support for fast input and navigation.

---

## How to Use

1. **Enter a puzzle**  
   Click any empty cell and press a digit (`1–9`) on your keyboard, or tap the number buttons on the panel. To pencil‑mark candidates, turn on `Notes` mode (or press `N`), then click a cell and enter digits.

2. **Solve**  
   Click the **Solve Puzzle** button. The solver will run and, if a solution exists, display the completed grid along with statistics (time, placements, backtracks) and a uniqueness badge.

3. **Watch the search**  
   After solving, click **Watch Solving Process** to replay every step of the backtracking search. Use the playback controls to pause, change speed, or skip to the end.

4. **Edit or reset**  
   Use **Clear All** to start over, or load one of the built‑in sample puzzles. At any time, click **Edit Puzzle** to return to the input mode and modify clues.

---

## Keyboard Shortcuts

| Key                           | Action                                       |
|-------------------------------|----------------------------------------------|
| `1` – `9`                     | Fill selected cell with digit                |
| `0` / `Backspace` / `Delete`  | Erase selected cell (clear digit or notes)   |
| `↑` / `↓` / `←` / `→`         | Move selection (wraps at edges)              |
| `N`                           | Toggle Notes (pencil‑mark) mode              |
| `Shift` + `1–9`               | Toggle a note in the selected cell           |
| `Esc`                         | Deselect current cell                        |

---

## How the Solver Works

The solver is a **backtracking depth‑first search** over the space of legal Sudoku grids. It is optimised with two key techniques:

### 1. Bitmask Constraint Propagation
Each row, column, and 3×3 box maintains a 9‑bit integer mask where bit `d‑1` is set if digit `d` is already used. The legal candidates for an empty cell are computed with a single bitwise expression:
cand = 0x1FF & ~(rowMask | colMask | boxMask)

This makes constraint checks **O(1)** and avoids any loops or set allocations.

### 2. MRV Heuristic (Minimum Remaining Values)
Instead of filling cells in left‑to‑right order, the solver always chooses the empty cell with the **fewest legal candidates**. This drastically prunes the search tree:
- A cell with one candidate is a **forced move** – effectively free constraint propagation.
- A cell with zero candidates proves the branch dead immediately.

On typical puzzles, MRV reduces the search from millions of nodes to a few hundred (or even zero backtracks for easy puzzles).

### 3. Backtracking DFS
The solver recursively tries each candidate digit in the chosen cell, updates the three masks, and recurses. If a dead‑end is reached, it unplaces the digit (restoring the masks with bitwise AND‑NOT) and tries the next candidate. Every placement and rollback is recorded for the **replay** feature.

### 4. Uniqueness Verification
After finding the first complete grid, the search continues until either a second solution is found or the entire space is exhausted (capped at 2 solutions). This determines whether the puzzle has a **unique solution** – the standard requirement for a proper Sudoku.

---

## Complexity

- Worst‑case (generalized Sudoku) is exponential (NP‑complete), but MRV ordering makes practical 9×9 puzzles tiny.
- Typical solve time: **< 1 ms** for easy puzzles, a few milliseconds for the hardest known grids.
- Memory usage: **O(81)** for the grid plus **O(depth)** for the recursion stack.

---

## Technology Stack

- **Pure vanilla JavaScript** – no frameworks, no external libraries.
- **HTML5 + CSS3** – fully responsive, works on desktop and mobile.
- **All logic is client‑side** – no server requests, no tracking.

---

## Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/sudoku-solver.git
   cd sudoku-solver
2. Open index.html in your browser – that’s it. No build tools or dependencies are required
3. (Optional) Serve with a local HTTP server for better performance:
   python -m http.server 8000
 or use any static server of your choice
