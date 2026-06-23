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

## 3. Validate PersistentVolume and PersistentVolumeClaim are bound

```bash
kubectl get pv
kubectl get pvc -n app
```

Both must show status `Bound`.

---

## 4. Validate PVC is mounted at `/app/data`

```bash
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')
kubectl exec -n app "$POD" -- df -h /app/data
```

Expected: filesystem is mounted at `/app/data`.

---

## 5. Validate ConfigMap is mounted as files in `/app/configs`

```bash
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')
kubectl exec -n app "$POD" -- ls /app/configs
```

Expected: each key from `app-config` ConfigMap appears as a separate file.
Files are listed in **alphabetical order** — that is the expected order (e.g. `APP_ENV`, `APP_PORT`, `DB_HOST`, `DB_NAME`, `DB_PORT`).

To read a specific value:

```bash
kubectl exec -n app "$POD" -- cat /app/configs/APP_ENV
```

---

## 6. Validate Secret is mounted as files in `/app/secrets`

```bash
POD=$(kubectl get pod -n app -l app=todoapp -o jsonpath='{.items[0].metadata.name}')
kubectl exec -n app "$POD" -- ls /app/secrets
```

Expected: each key from `app-secret` Secret appears as a separate file.

To read a secret value:

```bash
kubectl exec -n app "$POD" -- cat /app/secrets/db-user
```