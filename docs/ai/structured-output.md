# Structured Output

Primary source: `packages/contracts.ts`.

```ts
type Evidence = { ref: string; text: string };
type ProposalOutput = {
  text: string;
  citations: Evidence[];
  model: string;
  mode: 'extractive' | 'model';
};
```

Every provider — extractive or Ollama — returns this exact shape. `mode`
distinguishes a deterministic extraction from an actual model call, so
downstream review and any future clinical evaluation can segment results
by provider type without guessing from `model`'s free-text value. Citations
(`Evidence[]`) are `{ref, text}` pairs the review pipeline validates against
real record IDs/versions before the proposal can be accepted — see
[evidence.md](evidence.md).

The Ollama adapter's structured-output request to `/api/chat` constrains
the model's response to this same shape at the transport layer, rather than
relying on free-text parsing — reducing (not eliminating) the chance of an
unparseable or malformed response reaching the review pipeline.
