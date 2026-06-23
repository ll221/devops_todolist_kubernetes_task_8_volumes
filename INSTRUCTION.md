# INSTRUCTION.md

## 1. Create the cluster and deploy everything

```bash
chmod +x bootstrap.sh
./bootstrap.sh
```

---

## 2. Validate app is running

```bash
kubectl get pods -n app -o wide
```

All pods must have status `Running`.

---

## 3. Validate ConfigMap is mounted as files in `/app/configs`

```bash
# Pick any running pod
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')

# List files inside /app/configs — one file per ConfigMap key
kubectl exec -n app "$POD" -- ls /app/configs

# Read a specific config value
kubectl exec -n app "$POD" -- cat /app/configs/APP_ENV
```

Expected: each key from `app-config` ConfigMap appears as a separate file.

---

## 4. Validate Secret is mounted as files in `/app/secrets`

```bash
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')

# List files inside /app/secrets — one file per Secret key
kubectl exec -n app "$POD" -- ls /app/secrets

# Read a secret value
kubectl exec -n app "$POD" -- cat /app/secrets/db-user
```

Expected: each key from `app-secret` Secret appears as a separate file.

---

## 5. Validate PersistentVolume and PersistentVolumeClaim are bound

```bash
kubectl get pv
kubectl get pvc -n app
```

Both must show status `Bound`.

---

## 6. Validate volume is mounted at `/app/data`

```bash
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')
kubectl exec -n app "$POD" -- df -h /app/data
```