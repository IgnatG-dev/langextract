# Upstream Cherry-Pick Tracker

Tracking upstream PRs from [google/langextract](https://github.com/google/langextract/pulls) for cherry-picking into the managed fork (`ignatg/langextract`).

**Fork version:** 1.1.1 (based on upstream `3638fe4`)
**Branch:** `main` (cherry-picks applied here)
**Last reviewed:** 2026-02-19

---

## Already Included in Fork

These fixes are already part of the fork's `main` branch:

| PR | Title | Commit |
|----|-------|--------|
| #284 | Multi-language tokenizer support (Unicode & Regex) | `0c1af87` |
| #261 | Enable `suppress_parse_errors` parameter in resolver_params | `6e36c37` |
| #306 | Cross-chunk context awareness for coreference resolution | `3638fe4` |
| #300 | Handle non-Gemini model output parsing edge cases | `4882369` |
| N/A | Resolver dict-in-text graceful skip (custom fix) | `cf80fba` |

---

## HIGH PRIORITY — Cherry-Pick Now

| Status | PR | Title | Impact | Notes |
|--------|----|-------|--------|-------|
| [x] | [#351](https://github.com/google/langextract/pull/351) | Fix: Matching built-in providers fails if specified by name (Fixes #335) | Ensures `providers.load_builtins_once()` runs before provider resolution. Affects our `langextract-mistral` plugin loading. | Size XS. Applied manually. |
| [x] | [#349](https://github.com/google/langextract/pull/349) | Fix: Correctly load built-in providers when using explicit 'provider' in ModelConfig | Related to #351 — same provider loading issue from a different angle. | Size S. Same fix as #351. |
| [x] | [#327](https://github.com/google/langextract/pull/327) | Add `require_grounding` parameter to filter ungrounded extractions | Filters extractions where `char_interval is None` (hallucinated from few-shot). Directly improves extraction quality for our 3-pass, 12-class setup. | Size M. Fixes #209. |
| [x] | [#375](https://github.com/google/langextract/pull/375) | Fix: continue extraction when chunk resolve/alignment fails | Adds opt-in `suppress_parse_errors` at annotation level to skip per-chunk failures. Complements our resolver dict-skip fix. | Applied as `c2bd1bb`. Merged with our `require_grounding` parameter from PR #327. |
| [x] | [#374](https://github.com/google/langextract/pull/374) | Fix: improve robustness to Unicode and malformed JSON | Normalizes CJK radicals, handles trailing commas, multiple fenced blocks in LLM output. Resilience for multilingual contracts. | Applied as `4028976`. Resolver conflict resolved manually (added unicode normalization to correct locations). Test indentation fixed for 4-space convention. |

## MEDIUM PRIORITY — Monitor / Evaluate

| Status | PR | Title | Impact | Notes |
|--------|----|-------|--------|-------|
| [x] | [#350](https://github.com/google/langextract/pull/350) | Fix incorrect `char_interval` for non-ASCII text (Fixes #334) | Fixes `RegexTokenizer` merging Latin + CJK characters. Uses regex V1 set subtraction to separate CJK scripts from Latin in token patterns. | Applied manually. Adds `_CJK_SCRIPTS`, `_CJK_PATTERN`, and modifies `_LETTERS_PATTERN` with V1 set subtraction. 142→421 tests pass (new retry tests included). |
| [x] | [#257](https://github.com/google/langextract/pull/257) | Add retry mechanism for transient API errors (503, 429, timeouts) | Exponential backoff with jitter for transient LLM failures. Chunk-level retry in annotation pipeline preserves successful chunks. | Applied via `git apply --reject` + manual conflict resolution. New files: `retry_utils.py` (278 lines), `retry_utils_test.py` (300 lines). Modified: `annotation.py`, `extraction.py`, `gemini.py`, `annotation_test.py`. Complementary to litellm's provider-level `num_retries`. |
| [x] | [#356](https://github.com/google/langextract/pull/356) | Remove duplicate `model_id` assignment in `factory.create_model()` | Tiny cleanup (XS). Low risk, easy cherry-pick. | Applied as `5e0e65c`. |
| [ ] | [#32](https://github.com/google/langextract/pull/32) | Multi-language tokenizer support | **Already in fork** as #284. | Skip — already included. |

## APPLIED — Previously Low Priority

These PRs were previously classified as low priority but have been applied for completeness.

| Status | PR | Title | Impact | Notes |
|--------|----|-------|--------|-------|
| [x] | [#369](https://github.com/google/langextract/pull/369) | Fix format of `create_provider_plugin.py` | Script formatting cleanup. | Applied as `8a90d32`. |
| [x] | [#362](https://github.com/google/langextract/pull/362) | Comprehensive edge case tests for prompt validation | Test-only: empty text, unicode/special chars, very long text. Supersedes #317. | Applied as `b2074c1`. |
| [x] | [#317](https://github.com/google/langextract/pull/317) | Edge case tests for prompt validation | Subset of #362. Applied first, then superseded. | Applied as `335983f`. |
| [x] | [#359](https://github.com/google/langextract/pull/359) | Consolidate JSON serializer in Gemini batch | DRY: removes duplicate `_json_default` in `_upload()`, uses module-level `_json_serializer`. | Applied as `b7cc718`. |
| [x] | [#352](https://github.com/google/langextract/pull/352) | Fix batch inference crash (GCS cache TypeError) | Adds `_json_serializer()` for enum/dataclass in `_compute_hash`. | Applied as `e8e58d4`. |
| [x] | [#329](https://github.com/google/langextract/pull/329) | Add 11 Ollama model name regex patterns | ministral, devstral, nemotron, granite, glm, rnj, olmo, smollm, lfm, falcon, yi. | Applied as `dc778d1`. |
| [x] | [#326](https://github.com/google/langextract/pull/326) | Add `thinking_config` to Gemini handled_keys | Gemini 2.0 thinking support. | Applied as `866f8c2`. |
| [x] | [#310](https://github.com/google/langextract/pull/310) | Add Groq provider | New `groq.py` provider with model patterns and tests. | Applied as `586e824`. |
| [x] | [#305](https://github.com/google/langextract/pull/305) | Security fix for path traversal in validation script | Sanitizes directory traversal in `validate_community_providers.py`. | Applied as `139401c`. |
| [x] | [#267](https://github.com/google/langextract/pull/267) | Ollama keep_alive at top level | Moves `keep_alive` from options to request body root. | Applied as `e88c035`. |
| [x] | [#241](https://github.com/google/langextract/pull/241) | Preserve reasoning_effort for GPT-5 models | Fixes reasoning_effort param being dropped. Adds `_normalize_reasoning_params`. | Applied as `8ba19bc`. |
| [x] | [#242](https://github.com/google/langextract/pull/242) | Add Ali DashScope API provider | New `dashscope.py` for Qwen models + example + tests. Skipped openai.py model selection changes (hardcoded API keys, overlapping with existing Azure/DashScope support) and black formatting commit. | Applied as `10ded4d`. |

## LOW PRIORITY — Not Relevant

| PR | Title | Why Skip |
|----|-------|----------|
| #360 | Fix: health check failure curl not found | Their Docker image, not ours |
| #354 | Added Langfuse Integration | We don't use Langfuse |
| #347, #346, #321 | GitHub Actions upgrades | CI-only |
| #331 | Relax provider from model | Superseded by #351/#349 |
| #325 | Use shields.io for DOI badge | Docs/badge only |
| #294 | Migrate to uv | Build tooling — our Docker uses pip |
| #271, #46, #40, #18 | Misc (merge spam, conda badge, Ollama security) | Not relevant |

---

## Cherry-Pick Log

| Date | PR | Commits | Result | Branch |
|------|----|---------|--------|--------|
| 2026-02-17 | #351/#349 | Manual apply | factory.py: moved `load_builtins_once()`/`load_plugins_once()` before provider conditional | main |
| 2026-02-17 | #327 | Manual apply | extraction.py: added `_filter_ungrounded_extractions()` + `require_grounding` param | main |
| 2026-02-18 | #375 | `c2bd1bb` | annotation.py: `suppress_parse_errors` at annotation level, merged with `require_grounding` from #327 | main |
| 2026-02-18 | #374 | `4028976` | resolver.py: Unicode normalization (CJK radicals), trailing commas, multiple fenced blocks | main |
| 2026-02-18 | #350 | Manual apply | tokenizer.py: CJK script separation via regex V1 set subtraction (`_CJK_SCRIPTS`, `_CJK_PATTERN`) | main |
| 2026-02-18 | #257 | Manual apply | retry_utils.py (new), annotation.py, extraction.py, gemini.py: transient error retry with exponential backoff + jitter | main |
| 2026-02-19 | #356 | `5e0e65c` | factory.py: removed duplicate `model_id` assignment | main |
| 2026-02-19 | #369 | `8a90d32` | scripts/create_provider_plugin.py: formatting fix | main |
| 2026-02-19 | #329 | `dc778d1` | patterns.py: 11 new Ollama model regex patterns | main |
| 2026-02-19 | #267 | `e88c035` | ollama.py: `keep_alive` moved to top-level request body | main |
| 2026-02-19 | #305 | `139401c` | validate_community_providers.py: path traversal sanitization | main |
| 2026-02-19 | #326 | `866f8c2` | gemini.py: `thinking_config` added to `handled_keys` | main |
| 2026-02-19 | #352 | `e8e58d4` | gemini_batch.py: module-level `_json_serializer()` for cache hashing | main |
| 2026-02-19 | #359 | `b7cc718` | gemini_batch.py: consolidated JSON serializer, removed duplicate `_json_default` from `_upload()` | main |
| 2026-02-19 | #317 | `335983f` | prompt_validation_test.py: edge case tests (empty, unicode, long text) | main |
| 2026-02-19 | #362 | `b2074c1` | prompt_validation_test.py: comprehensive edge case tests superseding #317 | main |
| 2026-02-19 | #242 | `10ded4d` | dashscope.py (new), dashscope_example.py, test_dashscope_integration.py: Ali DashScope provider | main |
| 2026-02-19 | #310 | `586e824` | groq.py (new): Groq provider with model patterns and tests | main |
| 2026-02-19 | #241 | `8ba19bc` | openai.py: `_normalize_reasoning_params()` for GPT-5 reasoning_effort preservation | main |

---

## Upstream Sync Schedule

- **Frequency:** Monthly, or when Google merges a cherry-picked PR
- **Runbook:** See [UPSTREAM_SYNC.md](./UPSTREAM_SYNC.md)
- **Last sync:** N/A (initial setup)
