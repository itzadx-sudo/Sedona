# Configuration

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
