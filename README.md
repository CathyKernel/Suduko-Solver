# Suduko-Solver
A dependency-free, single-file Sudoku solver. Enter any puzzle, solve it instantly with abacktracking depth-first search (MRV heuristic + bitmask constraint propagation), watch thesolving process step by step, and verify solution uniqueness — 100% in your browser,nothing ever leaves your machine.

How to Use
Enter the puzzle — click a cell and type 1–9, or use the number pad. Press N to toggle Notes mode and pencil-mark candidates; with fewer than 17 clues the app warns you that multiple solutions will exist.
Solve — press Solve Puzzle. The solver first validates your clues for conflicts, then searches for a solution and probes for a second one to test uniqueness. Contradictory or unsolvable grids are reported with a clear explanation instead of hanging.
Read the result — the stats panel reports solve time, placements, backtracks, and clue count; the uniqueness badge tells you whether the puzzle is properly posed.
Replay the search — press Watch Solving Process to watch the algorithm think: green flashes mark placements, red flashes mark backtracks.
Keyboard Shortcuts
Key	Action
1 – 9	Fill selected cell
0 / Backspace / Del	Erase selected cell
↑ ↓ ← →	Move selection (wraps left/right)
N	Toggle Notes (pencil-mark) mode
Shift + 1–9	Toggle a single note in the selected cell
Esc	Deselect cell
Sample Puzzles
Available from the Load Sample menu:

Puzzle	Difficulty	Clues	Notes
Classic Evening Daily	Easy	30	The classic newspaper grid — pure constraint propagation, almost no backtracking
AI Escargot	Hard	23	Discovered by Arto Inkala in 2006 — famously among the hardest puzzles ever created
Norvig's Gauntlet	Expert	17	From Peter Norvig's essay — a pathological grid that forces the search tree to branch deeply
How the Solver Works
The engine is a classic backtracking depth-first search over the space of legal grids, accelerated with two techniques that turn an NP-sized brute-force problem into a millisecond-scale search.

1. Bitmask Constraint Tracking
Every row, column and 3x3 box keeps a 9-bit integer — bit d-1 set means digit d is already used in that unit. The legal candidates of any cell are computed in a single expression:

cand = 0x1FF & ~(rowMask | colMask | boxMask)
No loops over 9 cells, no sets, no allocations — three ORs, one NOT and one AND make each constraint check effectively O(1).

2. MRV Heuristic
Instead of filling cells left-to-right, the solver always branches on the empty cell with the Minimum Remaining Values — the fewest legal candidates:

a cell with one candidate is a forced move — free constraint propagation;
a cell with zero candidates proves the branch dead instantly.
This single decision rule shrinks the search tree from millions of nodes to a few hundred on typical puzzles.

3. Backtracking DFS
Place a digit, update the three unit masks, recurse. If the subtree fails, the digit is removed (masks restored with bitwise AND-NOT) and the next candidate is tried. Every placement and rollback is recorded as a step, which powers the replay feature.

Search Loop in Pseudocode
search():  if board is complete:            # 81 digits, all constraints hold      record solution; return true  cell <- empty cell with FEWEST candidates   # MRV heuristic  if candidates(cell) = empty:     # contradiction detected early      prune this branch  for d in candidates(cell):       # iterate set bits of the 9-bit mask      place d; rowMask |= d; colMask |= d; boxMask |= d      if search(): return true     # descend depth-first      remove d; restore masks      # <- backtracking  return false                     # all candidates failed
Uniqueness Verification
After the first solution is found, the search continues until a second complete grid appears or the space is exhausted (capped at 2 solutions). This is how the app distinguishes a proper puzzle — exactly one valid completion — from an underconstrained one, using the same solver with no extra code paths.
