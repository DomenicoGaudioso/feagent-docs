---
layout: default
title: "01 - Installation"
parent: English
nav_order: 1
---

# 01 - Installation

feagent is a pure-Python package: it runs wherever Python 3.9 or newer runs
(Windows, macOS, Linux) and needs only **numpy** and **scipy** for the solver.
Everything else (Excel workbooks, plots, Word reports, HDF5 results, the MCP
server) is an optional *extra* that you install only if you use it.

## Requirements

| | Minimum |
|---|---|
| Python | 3.9 (tested up to 3.13) |
| numpy | 1.24 |
| scipy | 1.10 |

## 1. Create a virtual environment (recommended)

```bash
python -m venv .venv
.venv\Scripts\activate           # Windows (PowerShell / cmd)
source .venv/bin/activate        # macOS / Linux
python -m pip install --upgrade pip
```

## 2. Install the package

The source code lives in a private repository; the package is distributed as
a **wheel** (`feagent-<version>-py3-none-any.whl`) or as a clone for those
who have access.

**From the wheel** (the `[all]` extra pulls Excel, plotting, report, HDF5 and MCP support):

```bash
pip install "feagent[all] @ file:///C:/Downloads/feagent-0.6.0-py3-none-any.whl"    # Windows
pip install "feagent[all] @ file:///home/user/feagent-0.6.0-py3-none-any.whl"      # macOS / Linux
```

(`pip install feagent-0.6.0-py3-none-any.whl` installs the base package only;
add the extras afterwards with `pip install pandas openpyxl plotly matplotlib
python-docx h5py`.)

**From a clone** (development, editable install):

```bash
git clone https://github.com/DomenicoGaudioso/feagent.git
cd feagent
pip install -e ".[all]"
```

**Directly from the repository** (needs access):

```bash
pip install "feagent[all] @ git+https://github.com/DomenicoGaudioso/feagent.git"
```

**With pipx**, to get the `feagent` command isolated from other projects:

```bash
pipx install "feagent[all] @ file:///C:/Downloads/feagent-0.6.0-py3-none-any.whl"
```

## Extras

| Extra | Packages | Needed by |
|-------|----------|-----------|
| `excel` | pandas, openpyxl | Excel models and results (the whole CLI workflow) |
| `plot` | plotly, kaleido | interactive HTML figures (`feagent plot`, `feagent.plotting`) |
| `matplotlib` | matplotlib | static PNG figures (`plot --png`) |
| `report` | matplotlib, python-docx | Word calculation report (`feagent report`) |
| `hdf5` | h5py | results in HDF5 (`.h5`) |
| `fast` | pypardiso | MKL Pardiso sparse solver for very large models |
| `mcp` | mcp | MCP server for AI agents (`feagent mcp`) |
| `all` | everything above except `fast` | |
| `dev` | `all` + pytest | running the test suite |

Extras can be combined: `pip install "feagent[excel,plot]"` (with the
`@ file:///...` or `-e .` form seen above).

## 3. Verify the installation

```bash
feagent doctor
```

`doctor` lists the Python interpreter, every optional package with the extra
that installs it, whether the `feagent` and `fg` commands are on the PATH,
the terminal capabilities and the result of a solver self-test. Alternatives:

```bash
feagent --version
python -c "import feagent; print(feagent.__version__)"    # 0.6.0
```

Then run your first analysis from a bundled example:

```bash
feagent examples cantilever --verify
```

## 4. Shell completion (optional)

```bash
feagent completion powershell >> $PROFILE     # PowerShell
feagent completion bash >> ~/.bashrc          # bash
feagent completion zsh  >> ~/.zshrc           # zsh
```

## Upgrading and removing

```bash
pip install --upgrade "feagent[all] @ file:///C:/Downloads/feagent-0.7.0-py3-none-any.whl"
pip install -e ".[all]"      # in a clone, after git pull (re-run only if dependencies changed)
pip uninstall feagent
```

## Running the tests (from a clone)

```bash
pip install -e ".[dev]"
python -m pytest tests -q
```

## Troubleshooting

### `'feagent' is not recognized as a command` (Windows) or `command not found`

The `Scripts` (Windows) or `bin` (macOS/Linux) folder of your Python
environment is not on the PATH. Either activate the virtual environment,
add the folder printed by `python -m feagent doctor` ("Cartella script") to
the PATH, or simply call the module:

```bash
python -m feagent doctor
```

### `ImportError: ... pandas` / `plotly` / `docx` (exit code 3)

The corresponding extra is not installed. The error message names the extra;
`feagent doctor` prints the complete `pip install "feagent[...]"` line.

### Colours or box characters look wrong

Use Windows Terminal (or any terminal with 24-bit colour and UTF-8) and a font
with good Unicode coverage such as Cascadia Code / Mono. `feagent --color
never` gives plain output; `NO_COLOR=1` does the same for every run.

### `ValueError: ref_vector parallel to beam axis`

For vertical elements (along Y) do not use a reference vector parallel to
the axis; leave it empty (the default picks global X for vertical elements)
or use `RefX = 1`. See [08 - Section Orientation](en-08-section-orientation.html).

### The Excel file cannot be written (`PermissionError`)

The workbook is open in Excel: close it, or write the results elsewhere with
`-o` / `--outdir`.

## Next steps

- [02 - Quick Start](en-02-quick-start.html): the first model with the Python API.
- [41 - CLI tutorial](en-41-cli-excel-tutorial.html): the first model from an Excel workbook, without code.
- [37 - Command-Line Interface](en-37-cli.html): reference of every command.
