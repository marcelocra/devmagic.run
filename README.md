# 🚀 DevMagic Environment

> [!NOTE]
> _If you just want to use it, run_:
> ```sh
> curl -fsSL https://devmagic.run/install | bash
> ```
> _(P.S.: It is always recommended to [see what you are running](https://devmagic.run/install) before doing so.)_

This directory contains the core configuration for the DevMagic development environment.

- `devcontainer.json`: The primary configuration file used by VS Code to define and create the development container.
- `docker-compose.yml`: Defines auxiliary services that can be run alongside the main development container.
- `Dockerfile`: A simple Dockerfile that can be used for custom builds, currently deprecated in favor of a direct `image` reference in `devcontainer.json`.

---

## 📦 Consumer Usage (for other repositories)

This repository is designed to be used as a **submodule** inside your projects, specifically mounted at `.devcontainer/`:

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

---

## Using Auxiliary Services (Ollama, Postgres, etc.)

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

---

## 🛠️ Maintainer Usage (developing this repo itself)

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
