# Running and troubleshooting

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
