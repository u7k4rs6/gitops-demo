# gitops-demo

Git is the source of truth for this cluster.

This repository holds the desired state of a small Kubernetes application.
Argo CD watches it and reconciles the cluster to match — nothing here is
applied by hand.

```text
Developer  ->  Git  ->  Argo CD  ->  Kubernetes
```

## Contents

```text
app/
├── deployment.yaml   nginx:1.27-alpine, replicas: 2
└── service.yaml      ClusterIP on port 80
```

## How changes are made

Do **not** run `kubectl apply` or `kubectl scale` against the cluster.
Change the YAML here instead:

```bash
# edit app/deployment.yaml -> replicas: 3
git commit -am "Scale application to three replicas"
git push
```

Argo CD detects the new commit and syncs the cluster. Git history becomes the
deployment history — every change is reviewable, diffable, and revertible.

## Drift

If someone changes the cluster directly, desired and actual state diverge:

```text
Git     (desired) = 2 replicas
Cluster (actual)  = 5 replicas
```

Argo CD reports the application as `OutOfSync` and — with self-heal enabled —
returns the cluster to whatever this repository says.

---

Session 20 · Monitoring, Observability & GitOps
