# Terms of Service

The legal side of an API, read off the provider's own terms page and made machine-readable: what you may use it for, what you may not, how long you may cache responses, whether you may redistribute them, what you must attribute, which law governs, and how much notice you get before the terms change or your access ends.

This is the schema behind the [Terms of Service property](https://apicommons.org/common/terms-of-service/)
in [API Commons](https://apicommons.org).

## Why it matters

API Commons was started to make the business side of API onboarding machine-readable. Finding an API is
the easy part; deciding whether you are allowed to use it — for this purpose, in this country, with this
data, by this kind of client — still means a person reading legal pages. When the answer is a document a
crawler, a catalog or an agent can read, discovery and onboarding can be automated end to end, and a
consumer can compare providers on the terms rather than on the marketing.

## Artifacts

- **[terms-of-service-json-schema.yml](terms-of-service-json-schema.yml)** — the JSON Schema (2020-12).
- **[terms-of-service-example-1.yml](terms-of-service-example-1.yml)** — a standalone document, as written to `all/<slug>/terms/<slug>-terms-of-service.yml`.
- **[terms-of-service-example-2.yml](terms-of-service-example-2.yml)** — the same document inside the `UsageTerms` apis.yml property envelope.
- **[validate.py](validate.py)** — validates any document against the schema.

Both examples describe **Example Weather API**, a fictional provider. The clauses are illustrative and are
not the terms of any real company.

## Fields

Every document carries the common envelope:

```yaml
terms_of_service: 0.1.0
provider: Example Weather API
aid: example-weather-api
url: <the page it was read from>
source_date: YYYY-MM-DD
x-apis-io-derivation:
  synthetic: false
  method: harvested
  read: YYYY-MM-DD
  tool: engine/business/harvest-business.py
  model: <model id>
```

and then:

| Field | Shape | Meaning |
|---|---|---|
| `effective_date` | `string / null` | Effective or last-updated date as written |
| `permitted_use[]` | `{summary, text}` | Explicitly permitted uses |
| `prohibited_use[]` | `{summary, text}` | Explicitly prohibited uses |
| `caching` | `{stance, max_duration, text}` | Whether and how long responses may be cached |
| `redistribution` | `{stance, text}` | Whether responses may be passed on |
| `attribution` | `{required, text}` | Whether attribution is required |
| `governing_law` | `{jurisdiction, text}` | Governing law |
| `liability_cap` | `{text}` | Limitation of liability |
| `termination_notice` | `{period, text}` | Notice before access is terminated |
| `change_notice` | `{period, text}` | Notice before the terms change |
| `rate_or_usage_limits_referenced` | `{text}` | Where the terms point at usage limits |

## The stance enum

Wherever a permission is described: `allowed | prohibited | conditional | unstated`. Every stance other
than `unstated` must carry a verbatim `text` — the schema enforces it. `unstated` means the page says
nothing, which is a valid, recorded result and is never filled in with a guess. Periods ("30 days",
"12 months") are copied as written and never normalized.

## Provenance: verbatim text, never synthetic

Only documents read from the provider's own page are published. There is no template fill. The page is
fetched by code; a model only transcribes rows from it; every row's `text` is checked word for word
against the fetched page and dropped if it does not match. `x-apis-io-derivation.synthetic` is always
`false` and `method` is always `harvested` — the schema rejects anything else.

Sources (apis.yml property types): TermsOfService, APITerms, TermsOfUse, Terms, DeveloperTerms*, APITermsOf*, AcceptableUsePolicy.

## Standard it profiles

[W3C ODRL 2.2](https://www.w3.org/TR/odrl-model/) — permissions, prohibitions and duties over an asset. The `permitted_use` / `prohibited_use` rows and the caching, redistribution and attribution stances map onto ODRL `permission`, `prohibition` and `duty`; the document does not use ODRL syntax, it keeps the provider's own words.

## Using it

```
pip install jsonschema pyyaml
python3 validate.py terms-of-service-example-1.yml terms-of-service-example-2.yml
```

Exits non-zero on any failure, so it can gate a pipeline.

## License

Two licenses, by kind of thing:

- **Artifacts** — the schemas and examples — are **[CC BY-NC-SA 4.0](LICENSE)** (Attribution–NonCommercial–ShareAlike).
- **Code** — the validator — is **[Apache-2.0](LICENSE-CODE)**.

## Part of API Commons

A machine-readable building block from **[API Commons](https://apicommons.org)**. Related business-layer blocks:
[terms-of-service](https://github.com/api-commons/terms-of-service) ·
[privacy-policy](https://github.com/api-commons/privacy-policy) ·
[deprecation-policy](https://github.com/api-commons/deprecation-policy) ·
[compliance](https://github.com/api-commons/compliance) ·
[eligibility](https://github.com/api-commons/eligibility) ·
[ai-preferences](https://github.com/api-commons/ai-preferences) ·
[data-license](https://github.com/api-commons/data-license) ·
[service-level-agreement](https://github.com/api-commons/service-level-agreement) ·
[plans](https://github.com/api-commons/plans) ·
[rate-limits](https://github.com/api-commons/rate-limits)
