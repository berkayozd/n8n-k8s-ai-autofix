# n8n + Kubernetes Auto-Triage (with Gemini)

An automated incident triage & auto-fix workflow built on **n8n**, targeting a demo **Kubernetes guestbook** app.
It collects evidence (SVC/Endpoints/Deployment/RS/Logs/Events), runs LLM-based diagnosis (Gemini),
asks for approval in Slack, applies the fix, and verifies health post-fix.

✅ **What it fixes (scenarios)**

Bad image tag (ErrImagePull/ImagePullBackOff) → set valid image

Service selector mismatch → patch Service selector

Service targetPort mismatch → patch Service ports

If ingress cache causes stale routing, workflow optionally syncs ingress (nginx-controller reload).

🔁 **High-level flow**

HealthCheck → GatherLogs → AssembleEvidence → Gemini Diagnose → Slack Approval → Apply Fix

Post-fix health check → (optional) Ingress sync → Notify success/failure

📂 **Repository layout**

See /k8s, /tools/n8n, /docs.

🚀 **Quick start**

Deploy healthy baseline
```
kubectl apply -k k8s/overlays/good
```

Run n8n stack
```
docker-compose up -d
 ```

Verify
```
kubectl get deploy,svc,pods -n guestbook
```

🧪 **Demo overlays (break → let n8n fix)**

1) image-bad
```
kubectl apply -k k8s/overlays/image-bad
```

2) svc-bad (selector mismatch)
```
kubectl apply -k k8s/overlays/svc-bad
```

3) port-bad (targetPort mismatch)
```
kubectl apply -k k8s/overlays/port-bad
```
n8n flow: collects evidence → AI diagnosis → asks approval in Slack → applies patch → post-fix health check (and optional ingress sync).

⚙️ Environment

Copy and edit:
```
cp .env.example .env
```
.env keys (example):

SLACK_WEBHOOK_URL=...

PG_HOST=localhost
PG_PORT=5432
PG_DB=n8n_logs
PG_USER=postgres
PG_PASSWORD=...

KUBECONFIG=/home/node/.kube/config

📸 Evidence for docs
Place screenshots in docs/screenshots/:

📝 Notes
.env and .n8n/ are ignored by Git; share only .env.example.
Ingress sync step is guarded; only runs when needed.
