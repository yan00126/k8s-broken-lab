# k8s-broken-lab

A small Kubernetes troubleshooting lab on k3d (Mac, Apple Silicon).
broken.yaml deploys 4 Deployments (web, report, worker, cache), a Service and an Ingress with 9 planted bugs.
I fixed them one by one with kubectl get / describe / logs / exec.

| # | Symptom | Root cause | Fix |
|---|---------|------------|-----|
| 1 | report Pending (Insufficient cpu) | CPU request 64 on a 1-node cluster | lower the request |
| 2 | web ErrImagePull | image tag typo nginx:1.27-alpinee | fix the tag |
| 3 | web Running but 0/1 | readinessProbe on 8080, nginx listens on 80 | probe port 80 |
| 4 | Service has no Endpoints | selector app: webapp, Pod label app: web | selector app: web |
| 5 | Endpoints exist but connection refused | Service targetPort 8080 | targetPort 80 |
| 6 | worker CreateContainerConfigError | configmap "app-cfg" not found | match the ConfigMap name |
| 7 | worker starts, logs DB_PASSWORD is empty | secretKeyRef key "pasword" + optional: true | key "password" |
| 8 | cache OOMKilled (exit 137) | memory limit too small | raise the limit |
| 9 | Ingress returns nothing | ingressClassName nginx (cluster has traefik) + backend web-svc | traefik + backend web |

Note: some fixes were applied with kubectl patch/edit, so the yaml file may not contain every fix yet.
