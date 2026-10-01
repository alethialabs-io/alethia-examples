# Alethia examples

Runnable references for [Alethia](https://alethialabs.io). Each one is a real workload delivered by
the product to a cluster you own, not a diagram — you can point an Alethia environment at any
directory here and watch it converge.

## What it is

| Example | Shows | Path |
|---|---|---|
| **Online Boutique** | The isolation ladder: five environments at four isolation levels on **one** cluster | [`examples/online-boutique/`](./examples/online-boutique) |
| **BYO-IaC modules** | Customer OpenTofu run through Alethia's verification gate and state proxy — including one that is deliberately refused | [`iac/`](./iac) |

```
examples/<name>/          one example, self-contained
  base/                   the application, unmodified upstream where possible
  overlays/<env>/         one Kustomize overlay per environment
iac/                      OpenTofu modules for the BYO-IaC path
```

Each example directory is independent, so an environment points at exactly one overlay and owns
exactly what that overlay declares.

## Use this template

**Use this template** to create your own copy if you want to change an example. To run one as it
is, you do not need a copy: point an environment at this repository directly.

## Connect it in Alethia

An environment declares the repository and the subpath it delivers:

```bash
alethia project component add --project <project> --env prod --kind repositories \
  --set apps_destination_repo=https://github.com/alethialabs-io/alethia-examples \
  --set apps_path=examples/online-boutique/overlays/prod
```

The tutorial that walks through the Online Boutique example end to end:
[Deploy the enterprise demo](https://alethialabs.io/docs/tutorials/enterprise-demo).

## The contract

`apps_path` means the same thing on every placement mode — `dedicated`, `vcluster` and `namespace`
all deliver exactly the path they name. That is what lets five environments share one repository
without any of them adopting another's manifests.

Each example's own README states what its paths must keep being:
[`examples/online-boutique/README.md`](./examples/online-boutique/README.md) and
[`iac/README.md`](./iac/README.md).

### Why `iac/` sits at the root

It is referenced by fixed paths (`iac/drift/<cloud>`, `iac/blocked`) from Alethia's own end-to-end
suite, which has proven BYO-IaC cells resolving through them. Moving it is a rename with a blast
radius, so it stays put until that is done deliberately rather than as a side effect of tidying.

### CI

`.github/workflows/iac-validate.yml` runs on every push and pull request that touches `iac/`:
`tofu fmt -check` and `tofu validate` over every module, with `-backend=false`, so it needs no
credentials and no state.

## Versioning

This repository has no `TEMPLATE_VERSION` and no `CHANGELOG.md`.

## Licence

Apache-2.0. See [`LICENSE`](./LICENSE) and [`NOTICE`](./NOTICE).

## The starter templates

| Repository | What it is |
|---|---|
| [`alethia-starter-apps`](https://github.com/alethialabs-io/alethia-starter-apps) | the apps-destination repository — root manifest, overlays, add-ons |
| [`alethia-starter-chart`](https://github.com/alethialabs-io/alethia-starter-chart) | a minimal bring-your-own Helm chart |
| [`alethia-starter-ai`](https://github.com/alethialabs-io/alethia-starter-ai) | RAG, a vector DB, CPU model serving and batch queueing, split across both trust levels |
