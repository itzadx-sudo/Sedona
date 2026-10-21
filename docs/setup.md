# Setup

## Requirements

Both platforms need Node.js 18+ with npm, curl and internet access during setup.

| Platform | Additional requirements |
| --- | --- |
| Linux | NVIDIA GPU and driver. The tested deployment uses Ubuntu 20.04 LTS. |
| macOS | Apple Silicon and a running Ollama daemon. |

For Ollama Cloud models, sign in before starting Sedona:

```bash
ollama signin
```

Cloud models need internet during assessments. Local models run on the host after installation.

## First administrator

Set these variables before the first launch. Replace the password placeholder with your own
password of at least 12 characters, using three of uppercase, lowercase, digits and symbols.

```bash
export SEDONA_BOOTSTRAP_ADMIN_USERNAME="sedona-admin"
export SEDONA_BOOTSTRAP_ADMIN_PASSWORD="<your-password>"
export SEDONA_BOOTSTRAP_ADMIN_DISPLAY_NAME="Sedona Administrator"
./start.sh
```

Sedona reads these values only while the user database is empty. After the account is created:

```bash
unset SEDONA_BOOTSTRAP_ADMIN_USERNAME
unset SEDONA_BOOTSTRAP_ADMIN_PASSWORD
unset SEDONA_BOOTSTRAP_ADMIN_DISPLAY_NAME
```

There is no default account. Usernames must contain 3-64 characters. Set the username and password
together. The sample environment file lists configuration options; do not source it.

## Installation

Run from the project folder as a normal user:

```bash
./start.sh
```

The launcher installs missing project dependencies, starts the API and frontend, and prints the
browser URL. On macOS, start Ollama before running the launcher.

To install or check dependencies separately:

```bash
./scripts/bootstrap.sh
./scripts/bootstrap.sh --check
./scripts/bootstrap.sh --force
```

`--check` returns 1 when components are missing and 0 when they are ready. `--force` reruns setup.

| Step | Linux | macOS |
| --- | --- | --- |
| Python | Creates a project environment | Creates a project environment |
| Packages | Installs backend dependencies | Installs backend dependencies |
| Inference runtime | Installs CUDA and builds llama.cpp | Uses Ollama |
| Models | Downloads GGUF files | Pulls Ollama tags |
| Frontend | Installs npm packages | Installs npm packages |

Project dependencies are stored under `.runtime/`. Linux GGUF files are stored in `models/`.
Ollama manages its own model storage under `~/.ollama`.
