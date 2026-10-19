# url-service-config
Dynamic runtime configuration source for URL Service, published to AWS AppConfig.

## Layout
- `environments/<environment>/config.json`: the configuration published for that environment (`local` or `aws`).
- `schema/config-schema.json`: the JSON Schema (draft-07) every `config.json` must satisfy.
- `Jenkinsfile`: the shared `publishAppConfig` pipeline, which validates the config against the schema, publishes it as a
  new AppConfig hosted configuration version of the `runtime-settings` profile, and starts a canary deployment
  (ADR-0011).

## Shape
A config has exactly two objects, `featureFlags` (switches) and `settings` (live-tunable values). Both are currently
empty, and the schema is closed: a key that is not declared in it is rejected. To add a flag or setting, declare it in
`schema/config-schema.json` with its type and limits, and add it to the `config.json` files, in the same change as the
service code that reads it.

## Checking a change
The pipeline runs these, and they can be run locally with `pip install check-jsonschema==0.38.2`:

    check-jsonschema --check-metaschema schema/config-schema.json
    check-jsonschema --schemafile schema/config-schema.json environments/local/config.json
