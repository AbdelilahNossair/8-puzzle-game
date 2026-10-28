# Eight Puzzle Solver

A Python study of graph search on the 3 × 3 sliding puzzle. It implements DFS, BFS, uniform-cost search, and A* with four heuristics, plus scripts for generating solvable states and exporting comparisons.

## Puzzle conventions

The blank is `0`. The goal used by the code is **blank first**, rather than blank last:

```text
0 1 2
3 4 5
6 7 8
```

An action (`up`, `down`, `left`, `right`) moves the blank. Every action costs one.

## Setup and interactive demo

Use Python 3 and Git. The solver itself uses the standard library; pandas is needed for CSV experiments.

```bash
git clone https://github.com/AbdelilahNossair/8-puzzle-game.git
cd 8-puzzle-game
python3 -m venv .venv
source .venv/bin/activate
python -m pip install pandas
python eightpuzzle.py
```

On Windows activate with `.venv\Scripts\activate`. The demo starts from a random sequence of 25 legal moves, solves with A* and `h4`, prints the action list, and waits for Enter between displayed states. Random moves can undo one another, so 25 shuffle moves do not imply an optimal depth of 25.

## Use the solver from Python

```python
import eightpuzzle
import search

state = eightpuzzle.EightPuzzleState([1, 0, 2, 3, 4, 5, 6, 7, 8])
problem = eightpuzzle.EightPuzzleSearchProblem(state)
path, expanded, depth, fringe = search.aStarSearch(problem, search.h3)
for action in path:
    state = state.result(action)
assert state.isGoal()
print(path, expanded, depth, fringe)
```

The search routines return a four-item tuple: action list, node counter, path depth, and a fringe-related counter.

| Heuristic | Calculation |
| --- | --- |
| `h1` | Number of misplaced nonblank tiles |
| `h2` | Sum of Euclidean distances to goal positions |
| `h3` | Sum of Manhattan distances to goal positions |
| `h4` | Row/column mismatch variant; see caveat below |

## Reproduce the CSV experiments

```bash
python generate_scenarios.py
python automate.py
```

The generator enumerates all 9! permutations and retains even-inversion configurations, producing a headerless `scenarios.csv` with nine integers per row. It materializes permutations in memory.

The automation samples 383 scenarios for heuristic comparison, then independently samples another 383 for search-method comparison. Outputs are:

- `detailed_results_task2.csv` and `averages_results_task2.csv`: A* with h1–h4.
- `detailed_results.csv` and `averages_results.csv`: A* h3, DFS, BFS, and UCS.

## Code map

| File | Responsibility |
| --- | --- |
| `eightpuzzle.py` | State transitions, goal checking, search adapter, interactive demo |
| `search.py` | Algorithms and heuristic functions |
| `util.py` | Supporting stacks, queues, and priority queue |
| `generate_scenarios.py` | Exhaustive solvable-state CSV generation |
| `automate.py` | Experiment execution and pandas reports |

## Known limitations and interpretation

The implementations count nodes and fringe size differently. In particular, some fringe counters include explored states, and A* uses the priority queue's cumulative counter; these are **not directly comparable peak-memory measurements**. The h4 column condition compares against the row index, so it needs correction before claiming its theoretical properties. Prefer h3 for an understandable baseline.

There is no input validation or fast rejection of arbitrary unsolvable states. DFS may return very long paths. Experiments have no fixed random seed, and large batches can consume considerable memory and time. No benchmark scores are claimed here.

## Attribution and reuse

Files retain UC Berkeley Pacman AI educational-use notices, including their restrictions on publishing solutions. Credit the original authors and retain those notices; see [UC Berkeley AI](http://ai.berkeley.edu). No standalone repository license is present, so this README does not grant an MIT license.
