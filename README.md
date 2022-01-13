## Authenticating GKE deployment + secret to Google Cloud with service accounts

### Importing credentials as a Secret (backend-gcp.json key with permission pubusb etc for your backend service)
```
kubectl -n staging create secret generic staging-backend-gcp-key --from-file=key.json=backend-gcp.json
```

### Deployment
```
    spec:
      volumes:
      - name: staging-backend-gcp-key
        secret:
          secretName: staging-backend-gcp-key  
      containers:
      - name: staging-abdi
        image: nginxdemos/nginx-hello:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: staging-abdi
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/key.json
        volumeMounts:
        - name: staging-backend-gcp-key
          mountPath: /var/secrets/google
```
