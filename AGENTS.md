# Repository Guidelines

## Project Structure & Module Organization

This repository contains OCI Landing Zone configuration fragments for one environment under `test-one-oe/`. Each JSON file is a deployable layer:

- `oneoe_governance.json`: tag namespaces and governance primitives.
- `oneoe_iam.json`: compartments, identity domains, groups, and policies.
- `oneoe_network_hub_e.json`: hub/spoke networking configuration.
- `oneoe_observability_cis1*.json`: observability, notifications, events, service connectors, and optional flow logs.
- `oneoe_security_cis1*.json`: Cloud Guard, Vulnerability Scanning, and Security Zones.

There is no application source tree or test suite. Treat each JSON file as infrastructure source.

## Build, Test, and Development Commands

Use local validation before pushing or applying through OCI Resource Manager:

```sh
jq empty test-one-oe/*.json
```

Validates JSON syntax for all configuration fragments.

```sh
git diff --check
```

Checks for whitespace errors before commit.

```sh
git status --short
```

Confirms only intended files are staged. Do not commit `.DS_Store` or Terraform state.

## Coding Style & Naming Conventions

Use two or four spaces consistently within a JSON file; preserve existing alignment. Keep keys descriptive and stable because other fragments may reference them. Follow current patterns such as `CMP-LZ-...-KEY`, `PCY-...-KEY`, `SZ-RCP-...-KEY`, and `NOTT-...-KEY`.

Use environment names consistently. Current resources use `JED` / `me-jeddah-1`; avoid stale region labels such as `FRA` unless intentional.

## Testing Guidelines

Run `jq empty test-one-oe/*.json` after every edit. For cross-reference changes, verify that referenced compartment, topic, log group, recipe, and policy keys exist in the same or prerequisite files.

Before applying in OCI Resource Manager, review the Terraform plan. Removing a file can destroy resources from that file if they are tracked in state.

## Commit & Pull Request Guidelines

Commit messages should be short, imperative, and specific, matching the existing history. Examples:

- `Add CIS observability and security configurations`
- `Rename network configuration to JED`
- `Remove invalid OSMS service policy`

Pull requests should include purpose, changed layers, validation commands, and expected OCI plan impact. Call out destructive changes, service-limit risks, and controls such as Security Zones or Cloud Guard responders.

## Security & Configuration Tips

Do not commit tenancy secrets, API keys, private keys, state files, or local metadata. Replace placeholder notification emails before customer use. For customer deployments, apply layers incrementally: governance, IAM, security/observability pre-baseline, then network and advanced controls.
