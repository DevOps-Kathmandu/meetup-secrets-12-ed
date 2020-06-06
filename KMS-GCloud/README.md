# Using Google Cloud KMS for Helm Secret

### Cloud KMS KeyRing and CryptoKey
Doc: https://cloud.google.com/kms/docs/encrypt-decrypt

#### Create keyring. eg name: `helm-secrets` (first time only)
```
gcloud kms keyrings create helm-secrets  --location=global
```

#### Create CryptoKey within the keyring. eg name: `api` (first time only)
```
gcloud kms keys create api --location=global --keyring=helm-secrets --purpose=encryption
```

---------------

### Implementation on helm
* Encrypt current `secret.yaml` file in any service:
```
gcloud kms encrypt \
    --location=global  \
    --keyring=helm-secrets \
    --key=[service-name] \
    --plaintext-file=secret.yaml \
    --ciphertext-file=secret.yaml.enc
```
This creates new encrypted file `secret.yaml.enc` which will be Decrypted during docker build
* gitignore current `secret.yaml` file
* Commit `secret.yaml.enc` file


#### Adding values or editing secret file manually
* Decrypt it:
```
gcloud kms decrypt \
    --location=global \
    --keyring=helm-secrets \
    --key=[service-name] \
    --ciphertext-file=secret.yaml.enc \
    --plaintext-file=secret.yaml
```
* Edit the decrypted `secret.yaml` file
* Encrypt it again:
```
gcloud kms encrypt \
    --location=global  \
    --keyring=helm-secrets \
    --key=[service-name] \
    --plaintext-file=secret.yaml \
    --ciphertext-file=secret.yaml.enc
```
