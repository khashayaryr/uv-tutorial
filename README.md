# 🚀 Mastering `uv`: The 2025 Python Workflow on WSL

**`uv`** is an extremely fast Python package installer and resolver, written in Rust. It replaces `pip`, `poetry`, `pip-tools`, `virtualenv`, and `pyenv` with a single binary.

> **💡 Why it matters:** On WSL (Windows Subsystem for Linux), file I/O is expensive. `uv` uses a global cache and hardlinks, meaning it creates virtual environments in milliseconds without copying files.

---

## 🛠️ 1. Installation (WSL)

Treat your WSL instance like a standard Linux server.

```bash
# Install via official script
curl -LsSf [https://astral.sh/uv/install.sh](https://astral.sh/uv/install.sh) | sh

# Restart shell, then verify
uv --version
```

---

## ⚡ 2. The Core Philosophy: "Run, don't Activate"

The biggest shift in 2025 is moving away from `source .venv/bin/activate`.

* **Old Way:** explicit state change in the terminal.
* **`uv` Way:** `uv run` automatically finds the environment and ensures it is synced before execution.

```bash
# Instead of activating, just run:
uv run python main.py
uv run uvicorn main:app --reload
```

---

## 🏗️ 3. Workflow: Starting a New Project

Stop using `mkdir` and `python -m venv`.

```bash
# 1. Scaffolds directory, pyproject.toml, and .venv
uv init my-backend
cd my-backend

# 2. Pin a specific Python version (downloads it automatically if missing)
uv python pin 3.12

# 3. Install dependencies (updates pyproject.toml & uv.lock)
uv add fastapi uvicorn pydantic
```

---

## 📥 4. Workflow: Cloning an Existing Repo

### Scenario A: The "Modern" Repo (Has `uv.lock`)
The repo has `pyproject.toml` and `uv.lock`. This is the dream scenario.

```bash
git clone <repo-url>
cd <repo>

# Installs Python, creates venv, installs packages in one go
uv sync
```

### Scenario B: The "Legacy" Repo (Has `requirements.txt`)
The repo only has `requirements.txt`. You have two options:

#### **Option 1: The "Quick & Dirty" (Treat `uv` like fast `pip`)**
Use this if you cannot change the project structure.
```bash
# 1. Manually create the environment
uv venv

# 2. Install using the pip interface
uv pip install -r requirements.txt

# 3. Run
uv run app.py
```

#### **Option 2: The "Upgrade" (Convert to Modern)**
Use this to migrate the project to `uv`.
```bash
# 1. Initialize uv structure
uv init

# 2. Import requirements into pyproject.toml + lockfile
uv add -r requirements.txt

# 3. (Optional) Delete the old file
rm requirements.txt
```

---

## 🔄 5. Switching Contexts

How do you switch environments between Project A and Project B? **You just `cd`.**

`uv` creates a `.venv` inside every project directory. There is no global state to manage.

1.  `cd ~/projects/backend-api` -> `uv run` uses Python 3.10
2.  `cd ~/projects/data-science` -> `uv run` uses Python 3.12

> **⚠️ Critical WSL Tip:** Always keep your projects in the Linux filesystem (`/home/user/projects`), NOT in the Windows mount (`/mnt/c/...`). The Windows file bridge kills performance.

---

## 📜 6. Cheat Sheet

| Task | Old Command (Pip/Venv) | **New Command (uv)** |
| :--- | :--- | :--- |
| **Create Project** | `mkdir` + `python -m venv` | `uv init <name>` |
| **Install Pkg** | `pip install pandas` | `uv add pandas` |
| **Remove Pkg** | `pip uninstall pandas` | `uv remove pandas` |
| **Install All** | `pip install -r requirements.txt` | `uv sync` (if lockfile exists) |
| **Run Script** | `source venv/bin/activate && python app.py` | `uv run app.py` |
| **Run Global Tool** | `pipx run black` | `uvx black` |
| **Update All** | *Manual Nightmare* | `uv lock --upgrade` |
