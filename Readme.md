# RandomQuotes-K8s

Sample ASP.NET Core app used as the source side of a GitOps deployment pipeline.

This repo is responsible for **application code + container image**.
Kubernetes manifests live in the separate GitOps repo:
**https://github.com/MazArslan/randomquotes-gitops**

## Pipeline

```
push to main ─▶ .github/workflows/ci.yml
                   1. Build multi-arch image (linux/amd64, linux/arm64)
                   2. Push to ghcr.io/mazarslan/randomquotes-k8s:sha-<short-sha>
                   3. Commit updated image tag to overlays/dev/kustomization.yaml in the gitops repo
                                       │
                                       ▼
                            ArgoCD detects the gitops commit
                            and reconciles the `dev` namespace
```

Prod is promoted by manually editing `overlays/prod/kustomization.yaml` in the
gitops repo and triggering a manual ArgoCD sync.

## Required secrets on this repo

| Secret | Purpose |
|---|---|
| `GITOPS_PAT` | Fine-grained PAT scoped to `MazArslan/randomquotes-gitops` with `contents:write`. Used by CI to commit image-tag bumps. |

`GITHUB_TOKEN` (provided automatically) is sufficient for pushing to GHCR.

## Local development

```sh
cd src/RandomQuotes.Web
dotnet run
# http://localhost:5000
```

## Acknowledgement

Originally based on
[OctopusSamples/RandomQuotes-K8s](https://github.com/OctopusSamples/RandomQuotes-K8s);
modified for a GitOps + ArgoCD pipeline.
