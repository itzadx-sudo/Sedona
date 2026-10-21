# Models

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
