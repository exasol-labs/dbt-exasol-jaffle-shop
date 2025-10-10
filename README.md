## dbt-exasol-jaffle-shop

This repository demonstrates how to run the classic `jaffle_shop` dbt project against an Exasol database. It walks through the full beginner workflow—from setting up tooling, creating an isolated Python environment with `uv`, installing the custom [`dbt-exasol`](https://github.com/mikhail-zhadanov/dbt-exasol) adapter, configuring `profiles.yml`, and finally running dbt commands.

### Prerequisites
- Exasol database credentials with permissions to create and modify objects in your target schema.
- Python 3.10 or newer available on your machine.
- The ability to install command-line tools and VS Code.

### 1. Install Visual Studio Code
- Download Visual Studio Code from <https://code.visualstudio.com/>.
- Launch VS Code and open this project (`File` → `Open Folder…`).

### 2. Install uv
[`uv`](https://docs.astral.sh/uv/latest/) is a fast Python packaging and virtual environment manager that will isolate your dbt dependencies.

```bash
# macOS
brew install uv

# Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows (PowerShell)
irm https://astral.sh/uv/install.ps1 | iex
```

Restart your shell so that the `uv` command is on your `PATH`.

### 3. Clone and enter this project

```bash
git clone https://github.com/mikhail-zhadanov/dbt-exasol-jaffle-shop.git
cd dbt-exasol-jaffle-shop
```

If you already have the repository, pull the latest changes instead.

### 4. Create and activate a virtual environment

```bash
uv venv .venv
source .venv/bin/activate  # On Windows use: .venv\Scripts\activate
```

`uv venv` creates a dedicated environment inside `.venv`. Activating it ensures that any Python packages you install stay isolated from your system Python.

### 5. Install dbt for Exasol

```bash
uv pip install git+https://github.com/mikhail-zhadanov/dbt-exasol.git
```

This installs the latest `dbt-exasol` adapter directly from GitHub (and pulls in `dbt-core` automatically).

### 6. Configure `profiles.yml`

dbt keeps connection information inside `~/.dbt/profiles.yml`. If the file does not already exist, create the directory and file:

```bash
mkdir -p ~/.dbt
touch ~/.dbt/profiles.yml
```

Add (or update) the following profile. Replace the placeholder values with your actual Exasol connection details:

```yaml
jaffle_shop:
  target: dev
  outputs:
    dev:
      type: exasol
      threads: 4                      # adjust for your workload
      dsn: localhost/nocertcheck:8563 # host[:options]:port
      user: sys
      password: exasol
      schema: dbt                     # target schema in Exasol
      database: exasol                # optional: logical database name
```

- `dsn` follows Exasol's DSN convention. Remove `/nocertcheck` if you use trusted certificates.
- Always keep secrets (user, password) secure—avoid committing them to version control.

Once the profile is in place, validate connectivity:

```bash
dbt debug
```

### 7. Run dbt against Exasol

```bash
dbt build   # run models, tests, seeds, snapshots as defined
```

You can also execute individual commands such as `dbt run` or `dbt test`.

### Next steps
- Explore the models in `models/` to understand how raw staging tables are transformed for analytics.
- Customize the project to fit your Exasol environment—add new sources, models, and tests.
- Consider configuring environment variables or secrets management to keep credentials out of plain text.
