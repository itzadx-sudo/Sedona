# Sedona setup and operations

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

## Knowledge base and accounts

1. Sign in as the administrator.
2. Open **Knowledge Base** and upload the approved policy PDFs and `BACKEND/HECVAT_Template.xlsx`.
3. Open **User Access** and create accounts for assessors and viewers.
4. Check readiness:

   ```bash
   curl "http://localhost:8080/health?strict=1"
   ```

Strict readiness returns HTTP 200 when the models and knowledge base are ready. An empty knowledge
base returns HTTP 503.

| Role | Access |
| --- | --- |
| Admin | Assessments, accounts and knowledge base |
| Assessor | Own assessment sessions, uploads, follow-ups and reports |
| Viewer | Completed sessions assigned by an administrator |

Public sign-up creates Viewer accounts. Passwords are stored as salted hashes. Sessions use cookies
and CSRF protection and expire after eight hours by default.

## Run an assessment

1. Upload a completed `.xlsx` or `.xlsm` HECVAT and enter the vendor or service name.
2. Start analysis. Sedona parses the controls, retrieves policy context and scores the answers.
3. Attach supporting PDFs to the assessment session as needed.
4. Answer, edit or skip follow-up questions. Saved answers can be paused and resumed.
5. Generate the report and download the PDF, PPTX and CSV.

Wait for the active assessment to finish before starting another. Closing the browser does not
stop the assessment. Open the session from History to continue. Cancelling an initial scan deletes
that session's data.

Uploads are capped at 50 MB. Supporting evidence is stored per session. The PDF and CSV contain
all controls; the PPTX is an executive briefing. Reports are frozen when the session is complete.

## Models and data flow

| Platform | Chat | Embeddings |
| --- | --- | --- |
| Linux | Local `gemma-3-4b-it` through llama.cpp | Local `nomic-embed-text-v1.5` |
| macOS | `gemma4:31b-cloud` through Ollama | Local `nomic-embed-text` |

The default macOS chat model runs on Ollama Cloud. Control text, vendor answers and retrieved
policy excerpts are sent to the cloud model. Parsing, chunking, embeddings and the vector store
run locally.

To change a running macOS deployment to a local chat model:

```bash
export SEDONA_LLM_MODEL="gemma3:4b"
ollama pull "$SEDONA_LLM_MODEL"
./start.sh --restart-api
```

Keep the model setting in the environment for later launches, or change the default in
`BACKEND/config.py`. Pull a model before selecting it.

To select a different cloud model:

```bash
export SEDONA_LLM_MODEL="gpt-oss:120b-cloud"
ollama pull "$SEDONA_LLM_MODEL"
./start.sh --restart-api
```

After changing `SEDONA_EMBED_MODEL`, re-ingest the policies and HECVAT template.
On Linux, the loaded chat weights are selected by `SEDONA_LLM_GGUF`.

## Service commands

| Command | Action |
| --- | --- |
| `./start.sh` | Start missing services and reuse healthy ones |
| `./start.sh --adopt` | Take ownership of reused Sedona services for shutdown |
| `./start.sh --restart-api` | Restart the API |
| `./start.sh --stop` | Stop this checkout's API and frontend |
| `./start.sh --stop --engines` | Also stop this checkout's llama.cpp servers |

Ctrl-C stops services owned by that launcher. Use `--stop` to stop services reused from an earlier
launch. Sedona never takes ownership of the shared Ollama daemon.

Frontend changes reload automatically. Backend and model-configuration changes need an API restart.

## Ports and storage

| Service | Default port |
| --- | --- |
| Frontend | 5173 |
| API | 8080 |
| Linux chat server | 8000 |
| Linux embedding server | 8001 |
| Ollama | 11434 |

The frontend listens on all interfaces by default. The API listens on localhost and is accessed
through the frontend proxy. The vector store runs in the API process.

| Path | Contents |
| --- | --- |
| `.runtime/` | Installed project runtime and setup markers |
| `models/` | Linux GGUF files |
| `chroma_db/` | Vector store |
| `auth/` | Local accounts and sessions |
| `reports/` | Assessment sessions and reports |
| `uploads_tmp/` | Staged uploads |
| `evidence/` | Session evidence |
| `logs/` | Service logs |

## Configuration

Set configuration through environment variables. Available options are listed in `.env.example`.

| Variable | Setting |
| --- | --- |
| `SEDONA_LLM_BACKEND` | `llama_cpp` or `ollama`; selected by platform by default |
| `SEDONA_LLM_MODEL` | Ollama chat model tag |
| `SEDONA_EMBED_MODEL` | Embedding model tag |
| `SEDONA_PYBIN` | Python interpreter |
| `SEDONA_DATA_DIR` | Persistent data directory |
| `SEDONA_LOG_DIR` | Log directory |
| `SEDONA_API_HOST` / `SEDONA_API_PORT` | API address and port |
| `SEDONA_FRONTEND_HOST` / `SEDONA_FRONTEND_PORT` | Frontend address and port |
| `SEDONA_LLM_HOST` / `SEDONA_LLM_PORT` | Chat service address and port |
| `SEDONA_EMBED_HOST` / `SEDONA_EMBED_PORT` | Embedding service address and port |
| `SEDONA_MODEL_DIR` | Linux GGUF directory |
| `SEDONA_LLM_GGUF` | Linux chat GGUF path |
| `SEDONA_COOKIE_SECURE` | Enable secure cookies when serving over HTTPS |
| `SEDONA_FORCE_CPU` | Request CPU inference on the llama.cpp backend |

## Troubleshooting

| Message or symptom | Action |
| --- | --- |
| npm is missing | Install Node.js 18+ with npm |
| Ollama is unreachable | Start Ollama.app or run `ollama serve` |
| Model is missing | Run `ollama pull` with the configured tag |
| Cloud model authentication fails | Run `ollama signin` |
| Knowledge base is empty | Upload policies and the HECVAT template |
| Python dependencies are missing | Run `./scripts/bootstrap.sh` |
| Linux GPU is missing | Check the NVIDIA driver, or explicitly select CPU mode |
| API fails to start | Check `logs/api.log` |
| Linux model server fails | Check `logs/llm.log` |
| Frontend fails | Check `logs/frontend.log` |

Ollama writes its own macOS logs under `~/.ollama/logs/`.
