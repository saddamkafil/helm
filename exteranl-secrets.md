Install External Secrets using helm: 
Add repository
```
helm repo add external-secrets https://charts.external-secrets.io
```

Install chart:
```
helm install external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
  # --set installCRDs=false
```

Step1: Create secrets in secretsmanager and copy name of the secrets 

Step 2:
2.1. create iam user with name external-secret-operator
2.2. create accesskey & secrets_key for above iam user

2.3. Create k8s secrets in k8s cluster using below manifest
```
---
apiVersion: v1
kind: Secret
metadata:
  name: aws-credentials
  namespace: default
type: Opaque
data:
  accessKeyID: BASE64_ENCODED_AWS_ACCESS_KEY_ID
  secretAccessKey: BASE64_ENCODED_AWS_SECRET_ACCESS_KEY
```

2.4. Cretae secrets store in k8s use below manifest
verify: kk get secretsstore
```
---
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: aws-secret-store ###whatever name you want to provide
  namespace: default
spec:
  provider:
    aws:
      service: SecretsManager
      region: us-west-2
      auth:
        secretRef:
          accessKeyIDSecretRef:
            name: aws-credentials
            key: accessKeyID
          secretAccessKeySecretRef:
            name: aws-credentials
            key: secretAccessKey
```
Step3: Create external secrets in k8 cluster using below manifest
verify: kk get externalsecrets

```
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: my-external-secret  # whatever name you want
  namespace: default
spec:
  refreshInterval: "1h"
  secretStoreRef:
    name: aws-secret-store  #SecretsStore.metadata.name
    kind: SecretStore
  target:
    name: my-k8s-secret ## external secrets will store k8 regular secrets with this name
    creationPolicy: Owner
  dataFrom:
  - extract:
      key: /cdp/github/atlantis/githubsecrets  ### secretsmanager secrets-name
```

Reference:
https://www.youtube.com/watch?v=EonWeoFPpvM&t=1237s      
