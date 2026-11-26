# Models

## Models and data flow

| Platform | Chat | Embeddings |
| --- | --- | --- |
| Linux | Local `gemma-3-4b-it` through llama.cpp | Local `nomic-embed-text-v1.5` |
| macOS | Local `gemma3:4b` through Ollama | Local `nomic-embed-text` |

All model inference, parsing, chunking, embeddings and vector search run locally on the host.
Ollama Cloud model tags (`*-cloud` and `*:cloud`) are rejected on macOS so control text, vendor
answers and retrieved policy excerpts never leave the machine.

To select a different local chat model on macOS:

```bash
export SEDONA_LLM_MODEL="gemma4:e4b-mlx"
ollama pull "$SEDONA_LLM_MODEL"
./start.sh --restart-api
```

Keep the model setting in the environment for later launches, or change the default in
`BACKEND/config.py`. Pull a local model before selecting it.

After changing `SEDONA_EMBED_MODEL`, re-ingest the policies and HECVAT template.
On Linux, the loaded chat weights are selected by `SEDONA_LLM_GGUF`.
