# Python Fundamentals — Package Managers and Build Tools

## 1. What Is Python?

Python is a high-level, general-purpose programming language known for its readable syntax and large ecosystem of libraries.

Python is widely used in:

- Automation and scripting
- DevOps engineering
- Cloud infrastructure automation
- Backend development and APIs
- Data processing
- Testing and monitoring tools
- CI/CD pipeline automation

### Why Do DevOps Engineers Need Python?

DevOps engineers use Python to automate repetitive tasks, interact with cloud APIs, process logs, manage infrastructure, and build automation tools.

Examples:

- Automating AWS resources using Boto3.
- Checking server health.
- Processing application logs.
- Calling REST APIs.
- Automating deployment tasks.
- Writing scripts for CI/CD pipelines.

### Example: Run a Python Program

Create a file named `app.py`:

```python
print("Hello from Python!")
```

Run it:

```bash
python3 app.py
```

Output:

```text
Hello from Python!
```

Check the installed version:

```bash
python3 --version
```

Check the executable location:

```bash
which python3
```

---

## 2. What Is a Python Interpreter?

The Python interpreter executes Python programs.

When you run:

```bash
python3 app.py
```

The Python interpreter reads and executes the program.

At a high level, CPython compiles Python source code into bytecode and executes that bytecode using the Python virtual machine. The exact implementation can differ between Python implementations.

### Python vs Node.js

| Feature | Python | Node.js |
|---|---|---|
| Purpose | Programming language and runtime ecosystem | JavaScript runtime |
| Program file | `.py` | `.js` |
| Execute program | `python3 app.py` | `node app.js` |
| Common package manager | pip | npm |
| Dependency metadata | `pyproject.toml`, often `requirements.txt` | `package.json` |
| Common uses in DevOps | Automation, scripting, cloud APIs | JavaScript tooling, automation, backend services |

---

## 3. What Is a Package?

A package is reusable code that provides functionality that can be used by other programs.

For example, instead of writing all HTTP request functionality yourself, you can install the `requests` package.

Common Python packages:

| Package | Purpose |
|---|---|
| `requests` | HTTP requests |
| `boto3` | AWS SDK for Python |
| `pytest` | Automated testing |
| `flask` | Lightweight web applications |
| `fastapi` | API development |
| `pandas` | Data manipulation |
| `black` | Python code formatting |
| `ruff` | Linting and code quality checks |

A Python package can contain multiple modules. A module is generally a Python source file that can be imported by another program.

---

## 4. What Is a Package Manager?

A package manager installs, updates, removes, and manages software packages and their dependencies.

In Python, **pip** is the standard package installer commonly used to install packages from the Python Package Index (PyPI).

### Check pip

```bash
python3 -m pip --version
```

Using `python3 -m pip` helps ensure that pip runs through the Python interpreter you selected.

### Common pip Commands

```bash
python3 -m pip install requests
python3 -m pip uninstall requests
python3 -m pip show requests
python3 -m pip list
python3 -m pip check
```

| Command | Purpose |
|---|---|
| `python3 -m pip install requests` | Install a package |
| `python3 -m pip uninstall requests` | Uninstall a package |
| `python3 -m pip show requests` | Display package information |
| `python3 -m pip list` | List installed packages |
| `python3 -m pip check` | Check installed dependencies for incompatibilities |

**Important:** On some Linux distributions, the system Python environment is externally managed. Installing packages globally with pip may be blocked or discouraged. Use a virtual environment for application dependencies.

---

## 5. Local Package Installation

For Python, local installation usually means installing packages inside a project-specific virtual environment rather than into the system Python environment.

### Step 1: Create a Project

```bash
mkdir python-app
cd python-app
```

Create a Python virtual environment:

```bash
python3 -m venv .venv
```

### Step 2: Activate the Virtual Environment

On Linux:

```bash
source .venv/bin/activate
```

After activation, the terminal often shows `(.venv)`.

### Step 3: Install a Package

```bash
python -m pip install requests
```

### Step 4: Verify the Installation

```bash
python -m pip show requests
python -m pip list
```

### Step 5: Use the Package

Create `app.py`:

```python
import requests

response = requests.get(
    "https://example.com",
    timeout=10
)

print("Status code:", response.status_code)
```

Run:

```bash
python app.py
```

This example requires network access to the website.

### Project Structure

```text
python-app/
├── .venv/
├── app.py
└── requirements.txt
```

### Why Use a Virtual Environment?

- Keeps project dependencies separate.
- Allows different projects to use different package versions.
- Avoids unnecessary changes to system Python packages.
- Makes dependency management easier for developers and CI/CD pipelines.

Deactivate the virtual environment:

```bash
deactivate
```

---

## 6. What Is a Global Package Installation?

A global installation places a package in the Python environment's shared installation location rather than a project-specific virtual environment.

Example:

```bash
python3 -m pip install --user some-package
```

This uses the current user's Python package installation location where supported. It is not necessarily a system-wide installation.

For command-line applications, tools such as `pipx` can install each application in an isolated environment while exposing its command.

Example, if pipx is installed:

```bash
pipx install some-cli-tool
```

### Local vs Global Installation

| Feature | Project virtual environment | Shared/user installation |
|---|---|---|
| Scope | One project | Shared or user-level environment |
| Dependency isolation | Yes | Usually less isolated |
| Typical use | Application libraries | Reusable CLI tools |
| Example | `python -m pip install requests` inside `.venv` | `pipx install black` |
| Recommended for application dependencies | Yes | Usually no |

**Best practice:** Use virtual environments for application dependencies. Use `pipx` for standalone Python command-line applications when appropriate.

---

## 7. What Is a Python Module?

A module is a reusable unit of Python code, usually a `.py` file.

Python modules can be grouped into three useful categories for foundational learning:

1. Built-in or standard-library modules.
2. Third-party packages and modules.
3. Your own application modules.

### A. Standard-Library Modules

Python provides a standard library with modules that do not normally need to be installed separately.

Examples:

| Module | Purpose |
|---|---|
| `os` | Operating system interfaces |
| `sys` | Python runtime information and system-specific functions |
| `pathlib` | Filesystem paths |
| `json` | JSON encoding and decoding |
| `subprocess` | Run external commands |
| `logging` | Application logging |
| `datetime` | Date and time operations |
| `re` | Regular expressions |
| `shutil` | High-level file operations |

Example:

```python
import os
import platform

print("Operating system:", platform.system())
print("Current directory:", os.getcwd())
```

Run:

```bash
python3 app.py
```

No `pip install os` or `pip install platform` is required.

These are standard-library modules.

### B. Third-Party Packages

Third-party packages are developed outside the Python standard library and are commonly installed through pip.

Example:

```bash
python -m pip install requests
```

Use it:

```python
import requests

print(requests.__version__)
```

The import name and the package's installation name are not always identical.

### C. Your Own Modules

Create `math_utils.py`:

```python
def add(a, b):
    return a + b
```

Create `app.py`:

```python
from math_utils import add

print(add(10, 20))
```

Run:

```bash
python3 app.py
```

Output:

```text
30
```

Here, `math_utils.py` is a module created by us.

---

## 8. What Is `requirements.txt`?

`requirements.txt` is a text file commonly used to list Python dependencies for an application.

Example:

```text
requests==2.32.3
pytest==8.3.5
```

The versions are illustrative examples; choose compatible versions for your project.

Install the listed packages:

```bash
python -m pip install -r requirements.txt
```

Generate a list of installed packages:

```bash
python -m pip freeze > requirements.txt
```

**Important:** `pip freeze` records installed distributions and their versions. Running it in a clean virtual environment helps avoid including unrelated packages.

---

## 9. What Is `pyproject.toml`?

`pyproject.toml` is a standardized configuration file used by modern Python projects.

It can define:

- Project metadata.
- Project dependencies.
- Build-system requirements.
- Build backend.
- Tool configuration, such as testing and linting settings.

Example:

```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"

[project]
name = "python-app"
version = "0.1.0"
dependencies = [
    "requests"
]
```

This is a minimal illustrative example. Real projects may need additional configuration.

### `requirements.txt` vs `pyproject.toml`

| Feature | `requirements.txt` | `pyproject.toml` |
|---|---|---|
| Main role | Lists packages for installation | Project metadata and tool/build configuration |
| Common use | Installing an environment's dependencies | Defining a modern Python project |
| Build-system configuration | No standard build-system role | Yes |
| Can list dependencies | Yes | Yes, using project metadata |
| Typical command | `pip install -r requirements.txt` | Depends on the project and build tool |

---

## 10. What Are Python Build Tools?

Build tools help turn source code into distributable artifacts, such as Python wheels and source distributions.

Python applications do not always require a separate compilation step before running. However, when packaging and distributing a Python project, build tools are often required.

### Common Python Build Tools

| Tool | Purpose |
|---|---|
| `build` | Front-end tool that builds Python distribution artifacts |
| `setuptools` | Build backend and packaging utilities |
| `hatchling` | Python build backend |
| `poetry` | Dependency management, packaging, and project workflows |
| `flit` | Packaging and building Python projects |
| `tox` | Automates testing across environments |
| `pytest` | Runs automated tests |
| `ruff` | Linting and code quality checks |

### Important Difference

- **Package manager / installer:** Installs dependencies, for example pip.
- **Build frontend:** Invokes a build backend, for example `build`.
- **Build backend:** Produces distribution artifacts, for example setuptools or hatchling.
- **Testing tool:** Checks application behavior, for example pytest.
- **CI/CD tool:** Automates the overall workflow, for example Jenkins or GitHub Actions.

Poetry provides multiple project-management functions, while `build` primarily invokes a build backend to create distribution artifacts.

---

## 11. Build a Python Package

Let's create a simple Python package and build it.

### Step 1: Create the Project

```bash
mkdir my-python-package
cd my-python-package
```

Create the following structure:

```text
my-python-package/
├── pyproject.toml
└── src/
    └── my_package/
        └── __init__.py
```

### Step 2: Add Python Code

Create `src/my_package/__init__.py`:

```python
def greet(name):
    return f"Hello, {name}!"
```

### Step 3: Configure the Build

Create `pyproject.toml`:

```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"

[project]
name = "my-python-package"
version = "0.1.0"
description = "A sample Python package"

[tool.setuptools.packages.find]
where = ["src"]
```

### Step 4: Install the Build Tool

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install build
```

### Step 5: Build the Package

```bash
python -m build
```

The command creates a `dist/` directory containing distribution artifacts, typically:

```text
dist/
├── my_python_package-0.1.0-py3-none-any.whl
└── my_python_package-0.1.0.tar.gz
```

The filenames use normalized package naming.

### What Are These Files?

- **Wheel (`.whl`):** A built distribution that can be installed with pip.
- **Source distribution (`.tar.gz`):** An archive containing source and packaging files from which a distribution can be built.

Install the wheel:

```bash
python -m pip install dist/my_python_package-0.1.0-py3-none-any.whl
```

Test the package:

```bash
python -c "from my_package import greet; print(greet('DevOps'))"
```

Expected output:

```text
Hello, DevOps!
```

---

## 12. What Is a Build Artifact?

A build artifact is a file or package produced by a build process.

Python examples:

- Wheel files (`.whl`).
- Source distribution archives (`.tar.gz`).
- Application archives or container images produced by later deployment steps.

A Python wheel is not the same as a Docker image. A wheel packages Python software for installation; a Docker image packages an application and its runtime environment into a container image.

### Why Build Artifacts Matter in DevOps

1. Source code is committed to Git.
2. CI checks out the code.
3. Dependencies are installed.
4. Tests and quality checks run.
5. A package or application artifact is built.
6. The artifact is stored or published.
7. The artifact is deployed to a target environment.

---

## 13. Python in a DevOps CI/CD Pipeline

Example workflow:

```text
Developer
   |
   v
GitHub Repository
   |
   v
CI Pipeline
   |
   +--> Select Python version
   |
   +--> Create virtual environment
   |
   +--> Install dependencies
   |
   +--> Run linting and tests
   |
   +--> Build package or application artifact
   |
   +--> Publish artifact or build Docker image
   |
   v
Deploy to Server / Cloud / Kubernetes
```

Example commands:

```bash
python3 --version
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m pytest
python -m build
```

The project must have the relevant dependencies, tests, and build configuration for these commands to succeed.

### How Python Helps DevOps Engineers

- Automates infrastructure tasks.
- Interacts with AWS through Boto3.
- Calls APIs and processes JSON.
- Automates server administration.
- Tests applications.
- Supports CI/CD workflows.
- Produces packages for distribution.
- Helps build monitoring and reporting scripts.

---

## 14. Troubleshooting Commands

```bash
python3 --version
which python3
python3 -m pip --version
python3 -m pip list
python3 -m pip show requests
python3 -m pip check
python3 -m venv .venv
python -m pip install -r requirements.txt
python -m pytest
python -m build
```

| Problem | What to check |
|---|---|
| `python3: command not found` | Python installation and PATH |
| `No module named pip` | Whether pip is installed for that interpreter |
| `No module named requests` | Active environment and package installation |
| `externally-managed-environment` | Use a project virtual environment |
| Permission denied | Environment and directory permissions |
| Build fails | `pyproject.toml`, build backend, and build dependencies |
| Works locally but fails in CI | Python version, dependency declarations, and environment configuration |

---

## 15. Key Takeaways

- Python is a programming language used extensively in automation and DevOps.
- The Python interpreter executes Python programs.
- pip is a common Python package installer.
- A virtual environment isolates project dependencies.
- Standard-library modules come with Python.
- Third-party packages are commonly installed using pip.
- Your own `.py` files can be imported as modules.
- `requirements.txt` commonly lists dependencies.
- `pyproject.toml` configures project metadata, build systems, and tools.
- Build tools create distributable artifacts such as wheels and source distributions.
- Testing tools such as pytest validate application behavior.
- CI/CD automates testing, packaging, and deployment.