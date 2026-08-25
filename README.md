# eval-frames

**FRAMES: Factual Retrieval and Multi-hop Evaluation Set**

> ⚠️ **Third-party eval.** This is a `register/` pointer in inspect_evals — the task code lives in an external repository of unaudited provenance and will execute on OpenEvalz infrastructure. Onboarding it is a security review, not a packaging task.

**Paper:** https://arxiv.org/abs/2409.12941

FRAMES evaluates multi-hop reasoning by requiring models to answer questions
that can only be resolved by chaining information across multiple Wikipedia
documents. Two task variants are provided:

- **frames_baseline**: all source documents are provided upfront; tests
  whether a model can reason across a full document set.
- **frames_socrates**: documents are withheld and the model must iteratively
  request them via a `request_document` tool. Retrieval is constrained to a
  per-sample allowlist of ground-truth source documents, preventing
  eval-awareness contamination. Scored with a decaying reward
  `accuracy × min(optimal_hops / actual_hops, 1)` that penalises
  unnecessary hops and hallucinated document requests.

## At a glance

| | |
|---|---|
| Upstream | [`register/frames`](https://github.com/UKGovernmentBEIS/inspect_evals/tree/main/register/frames) |
| Group | — |
| Total samples | 0 |
| Execution class | `plain` |
| Cost class | `low` |
| Flags | no sandbox, no network |
| Tags | reasoning, retrieval, multi-hop, Agent |

### Tasks

| Task | Samples |
|---|---|
| `frames_baseline` | 0 |
| `frames_socrates` | 0 |

### External assets

_None declared upstream._

## Running one problem

OpenEvalz is problem-level: the atomic unit is a single sample, not the whole eval.

```bash
inspect eval inspect_evals/frames_baseline \
  --sample-id "<sample-id>" \
  --model openai-api/trustedrouter/<model> \
  --token-limit 200000
```

> **Two things that bite here, both verified in Inspect's source.**
>
> 1. **`--cost-limit` does not work on this routing path.** Inspect only records cost when its
>    pricing table resolves the model, and `_model_info.py` strips only `azure|bedrock|vertex`
>    prefixes — so `trustedrouter/<model>` never resolves and the cap silently never binds. The
>    real spend cap is enforced **server-side by TrustedRouter** via the delegated key's
>    `limit_microdollars` and spend window. Use `--token-limit` as the in-process bound.
> 2. **`--sample-id` matches with `fnmatch`.** A glob silently selects many samples and only warns.
>    Always pass a literal id.

## Reproducibility

`bundle.template.json` is the contract. A run that cannot emit a complete bundle does not publish.
Every image is pinned by `sha256` digest and every dataset by revision.

## Licensing

OpenEvalz wrapper code in this repository is **Business Source License 1.1** (see `LICENSE`) —
Licensor Lore Hex Corp, Change Date four years from publication, Change License Apache 2.0, no
Additional Use Grant. Same terms as TrustedRouter. Source-available, not open source: you may read,
modify and make non-production use of it, but production use needs a commercial licence
(licensing@openevalz.com).

**The packaged evaluation is NOT relicensed.** The task code, dataset and container images come from
upstream under their own terms — inspect_evals is MIT (UK AI Security Institute), and individual
datasets and images carry their own, sometimes unstated, licences. BSL covers only the OpenEvalz
packaging around them. See `NOTICE.md`, which must be completed before this repo publishes anything.
