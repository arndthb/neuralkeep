# Experimental local AI integration

The reference system runs an OpenAI-compatible MLX inference endpoint on an Apple Silicon Mac. The Karakeep container reaches the host through `host.docker.internal`.

Illustrative environment variables:

```dotenv
OPENAI_API_KEY=local
OPENAI_BASE_URL=http://host.docker.internal:8091/v1
INFERENCE_TEXT_MODEL=REPLACE_WITH_YOUR_MODEL_ID
```

The API key value above is a placeholder for a local endpoint; never reuse it for an internet-facing service.

## Verification checklist

1. Confirm that the inference server is running and reachable from inside the Karakeep container.
2. Confirm `GET /v1/models` returns the intended model.
3. **Separately test an actual chat-completion request.** Listing models alone does not establish compatibility.
4. Confirm Karakeep's version supports the selected inference configuration.
5. Test tagging on a non-sensitive bookmark and inspect logs.
6. Only then consider bulk automatic tagging.

**Current status:** container-to-host API connectivity has been observed; successful end-to-end AI tagging has not yet been confirmed.

Local models can require substantial unified memory. Model identifiers, server ports and performance depend on the local installation.
