# 🚀 DevMagic Environment

> [!NOTE]
> _If you just want to use it, run_:
> ```sh
> curl -fsSL https://devmagic.run/install | bash
> ```
> _(P.S.: It is always recommended to [see what you are running](https://devmagic.run/install) before doing so.)_

## TL;DR

DevMagic can be used in **three different ways**:

1. **Standalone (recommended for most users)** — use this repo directly as a portable dev environment (no need to install Node, Python, Git, etc. on your host; just Podman/Docker + VS Code).
2. **Consumer** — embed it as a `.devcontainer` submodule inside your own projects.
3. **Maintainer** — work on `devmagic.run` itself, using its wrapper `.devcontainer`.

It also supports optional auxiliary services (Ollama, Postgres, Redis, etc.).

👉 Jump to: [Standalone](#-standalone-usage-portable-dev-environment) · [Consumer](#-consumer-usage-for-other-repositories) · [Auxiliary Services](#using-auxiliary-services-ollama-postgres-etc) · [Maintainer](#-maintainer-usage-developing-this-repo-itself)

## Overview

This repository contains the core configuration for the DevMagic development environment.
You can use it as a **standalone workspace**, as a **submodule in other projects**, or to
**develop DevMagic itself**.

## 💻 Standalone Usage (Portable Dev Environment) <a id="standalone"></a>

You can use this repository **directly as your dev environment**. This is useful if:

- You are on a fresh OS installation,
- You don’t want to install development tools (Git, Node, Python, etc.) on your host,
- You just want a temporary throw‑away workspace to hack on code.

### Requirements

- A container runtime (e.g. [Podman Desktop](https://podman-desktop.io) or Docker)
- [Visual Studio Code](https://code.visualstudio.com/) (or any devcontainer‑compatible editor)

### Getting Started

1. Clone this repository:

   ```bash
   git clone https://github.com/marcelocra/devmagic.run.git
   cd devmagic.run
   ```

   > 💡 If you don't have `git` installed locally, you can even use GitHub Desktop
   > or download the repo as a zip, since this environment itself provides Git.

2. Open the folder in VS Code and choose **“Reopen in Container.”**

3. You now have a fully featured dev environment with Git, Node, Python,
   and all other supported tools, **without installing anything else** on the host system.

### Temporary Workspace Workflow

- Use this repo as a personal dev terminal/workstation.  
- Whenever you need to work on another repo:

  ```bash
  git clone https://github.com/other/repo.git
  cd repo
  code .
  ```

- Each cloned repo automatically uses the same dev container setup.

This makes `devmagic.run` a **portable coding box** you can carry
between machines or use on a fresh OS in minutes.

## 📦 Consumer Usage (for other repositories) <a id="consumer"></a>

This repository is also designed to be used as a **submodule** inside your projects, specifically mounted at `.devcontainer/`:

```bash
git submodule add https://github.com/marcelocra/devmagic.run.git .devcontainer
```

After adding, your project will have:

```
your-project/
└── .devcontainer/        ← submodule
    ├── devcontainer.json
    ├── docker-compose.yml
    ├── Dockerfile
    └── README.md
```

From here, open the project in VS Code and "Reopen in Container."

## Using Auxiliary Services (Ollama, Postgres, etc.) <a id="aux"></a>

This environment is designed to be modular. The main dev container starts by default, and you can bring up additional services like `ollama` or `postgres` on demand.

This process starts **after** you have already opened your project in the dev container.

### Step 1: Open a Terminal in VS Code

Open a new terminal inside VS Code (`Terminal > New Terminal`). You will be running commands from within your main dev container.

### Step 2: Start an Auxiliary Service

Your `docker-compose.yml` file is in your workspace, and because you have Docker installed in your container (via the `docker-in-docker` feature), you can use the `docker compose` command.

To start the `ollama` service, for example, run:

```bash
docker compose --profile ai up -d
```

To start `postgres`, run:

```bash
docker compose --profile postgres up -d
```

- `--profile <name>`: This flag tells Compose to look inside your `docker-compose.yml` and only start the services marked with that profile name.
- `up -d`: This creates and starts the container(s) in the background.

### Step 3: Verify the Service is Running

You now have multiple containers running side-by-side. You can verify this by running:

```bash
docker ps
```

You will see your main devcontainer and the new service container(s). They are on the same Docker network and can communicate with each other using their service names (e.g., `ollama`, `postgres`).

### Step 4: Connect to the Service

From inside your main dev container, you can now access the service using its name as the hostname.

- **Ollama:** `http://ollama:11434`
- **Postgres:** `postgresql://codespace:DevTime2024!coding@postgres:5432/devdb`
- **Redis:** `redis://redis:6379`

### Step 5: Stopping a Service

When you are finished, you can stop the service(s) without affecting your main dev container.

```bash
# Stop ollama
docker compose --profile ai down

# Stop postgres
docker compose --profile postgres down
```

## 🛠️ Maintainer Usage (developing this repo itself) <a id="maintainer"></a>

Normally, this repository provides the contents of a `.devcontainer/` folder when used as a submodule inside consumer projects.

However, if you want to **develop this repository itself** inside a Dev Container:

1. Clone this repository:
   ```bash
   git clone https://github.com/marcelocra/devmagic.run.git
   cd devmagic.run
   ```

2. A `.devcontainer/devcontainer.json` wrapper file is included, which simply extends the root `devcontainer.json`.  
   VS Code will detect it and allow you to **“Reopen in Container”**.

3. This setup ensures:
   - **Consumers** see the expected `.devcontainer/` contents when using this repo as a submodule.
   - **Maintainers** can work on `devmagic.run` itself in a self‑hosted Dev Container without extra steps.
