# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Conway's Game of Life rendered in the terminal with `curses`. Python >=3.10, packaged via setuptools (`pyproject.toml`). The only dependency is `windows-curses` on Windows. There are no tests or linter config.

## Commands

- Set up (Windows): `venv\Scripts\activate`, then `pip install -e .`
- Run: `python -m game_of_life [-p PATTERN] [-a] [-v VIEW] [-g NUM_GENERATIONS] [-f FPS]`. Example: `python -m game_of_life -p Pulsar -g 30`. `--version` prints the version, and `-a` plays every pattern in `patterns.toml` in turn.
- Console script `game-of-life` points to `game_of_life.__main__:main` (same as `python -m game_of_life`).

## Architecture

Flow: `__main__.main()` calls `cli.get_command_line_args()`, then looks up the view class by name with `getattr(views, args.view)`. It loads the pattern(s) from `patterns.py` and calls `View(pattern, gen, frame_rate).show()`.

- `cli.py`: argparse only. `--pattern` choices come from the names in `patterns.toml`, and `--view` choices come from `views.__all__`. A new view class must be listed in `views.__all__` to be selectable.
- `patterns.py`: loads named patterns from `patterns.toml`. Each TOML table (`["Blinker"]`, `["Pulsar"]`, ...) has an `alive_cells` list of `[row, col]` pairs. They become a `Pattern` dataclass holding a `set` of `(row, col)` tuples. The file is re-read on every call. `tomllib` falls back to `tomli` on Python <3.11, which `pyproject.toml` installs conditionally.
- `grid.py`: `LifeGrid` wraps a `Pattern`. `evolve()` is a sparse, set-based step with no fixed board, so the universe is unbounded. It counts neighbors only around live cells, then applies the survival and birth rules. **It mutates `pattern.alive_cells` in place**, so evolving a grid changes the shared `Pattern` object. `as_string(bbox)` renders a viewport and is separate from the simulation.
- `views.py`: `CursesView` runs the loop inside `curses.wrapper`. It draws `LifeGrid.as_string(bbox)` and calls `evolve()` once per frame for `gen` generations, sleeping `1/frame_rate` between frames. If the terminal is too small, `curses.error` is re-raised as `ValueError`. `_show_pattern` in `__main__.py` catches all exceptions and prints them to stderr, so errors do not produce a traceback.

Coordinate conventions: cells are `(row, col)`, but `bbox` is `(start_col, start_row, end_col, end_row)`. The default bbox is `(0, 0, 20, 20)`, so cells outside that window are simulated but not drawn.

The package version is dynamic: `pyproject.toml` reads `game_of_life.__version__` via `[tool.setuptools.dynamic]`.

`patterns.toml` holds Blinker, Toad, Beacon, Pulsar, Penta Decathlon, Glider, Glider Gun and Bunnies. Many of them (Glider Gun, Bunnies, Glider) extend past the default 20x20 bbox. It is included as package data (`[tool.setuptools.package-data]`), so new data files need matching globs there.
