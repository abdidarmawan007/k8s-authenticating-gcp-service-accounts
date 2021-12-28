## Authenticating GKE deployment + secret to Google Cloud with service accounts

### Importing credentials as a Secret (backend-gcp.json key with permission pubusb etc for your backend service)
```
kubectl -n staging create secret generic staging-backend-gcp-key --from-file=key.json=backend-gcp.json
```

### Deploy example pubusb deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: staging
  name: staging-pubsub
spec:
  selector:
    matchLabels:
      app: staging-pubsub
  template:
    metadata:
      labels:
        app: staging-pubsub
    spec:
      volumes:
      - name: staging-backend-gcp-key
        secret:
          secretName: staging-backend-gcp-key
      containers:
      - name: staging-pubsub
        image: us-docker.pkg.dev/google-samples/containers/gke/pubsub-sample:v1
        volumeMounts:
        - name: staging-backend-gcp-key
          mountPath: /var/secrets/google
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/key.json
```
