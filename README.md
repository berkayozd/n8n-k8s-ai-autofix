# n8n + Kubernetes Auto-Triage (with Gemini)

An automated incident triage & auto-fix workflow built on **n8n**, targeting a demo **Kubernetes guestbook** app.
It collects evidence (SVC/Endpoints/Deployment/RS/Logs/Events), runs LLM-based diagnosis (Gemini),
asks for approval in Slack, applies the fix, and verifies health post-fix.

## What it fixes (scenarios)
1. **Bad image tag** (ErrImagePull/ImagePullBackOff) → set valid image
2. **Service selector mismatch** → patch Service selector
3. **Service targetPort mismatch** → patch Service ports
   - If ingress cache causes stale routing, workflow optionally syncs ingress (nginx-controller reload).

## High-level flow
- HealthCheck → GatherLogs → AssembleEvidence → Gemini Diagnose → Slack Approval → Apply Fix
- Post-fix health check → (optional) Ingress sync → Notify success/failure

## Repository layout
See `/k8s`, `/tools/n8n`, `/docs`.

## Quick start
# update .env, Slack webhook etc.
```bash
kubectl apply -k k8s/overlays/good
docker-compose up -d

