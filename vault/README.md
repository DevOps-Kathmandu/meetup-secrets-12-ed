# Secrets Management with Vault

## Installation
https://www.digitalocean.com/community/tutorials/how-to-securely-manage-secrets-with-hashicorp-vault-on-ubuntu-16-04

## Installation Sample Config with Filesystem Storage Backend
```
disable_cache = true
disable_mlock = true
ui = true
listener "tcp" {
    address = "0.0.0.0:8200"
    tls_disable = 1
}
storage "file" {
  path = "/vault/data"
}

```

## Init and unseal(for dev purpose)
```
vault operator init | sudo tee /etc/vault/init.file
egrep -m3 '^Unseal Key' vault-key.txt | cut -f2- -d: | tr -d ' ' | while read key; do   vault operator unseal ${key}; done
export VAULT_ADDR=http://0.0.0.0:8200
vault login [root-token]
```

## Browse UI
http://0.0.0.0:8200/ui

## AWS config
```
vault write aws/config/root   access_key=ACCESS_KEY  secret_key=SECRET_KEY  region=us-west-2
```
```
vault write aws/roles/ec2-role
	credential_type=iam_user
		policy_document=<<EOF
		{
		"Version": "2012-10-17",
		"Statement": [
				{
				"Effect": "Allow",
				"Action": "ec2:*",
				"Resource": "*"
				}
			]
		}
```

```
vault read aws/creds/ec2-role
vault lease revoke LEASE_ID
vault write aws/config/lease lease=24h lease_max=24h
vault lease renew LEASE_ID 3600
vault lease revoke -prefix aws/creds/s3-all-crud-role
```

# Learning Resource
Google Cloud IAM Generator: https://codelabs.developers.google.com/codelabs/hashicorp-vault-secrets-generate-service-account-credentials/index.html
Vault Agent with AWS: https://learn.hashicorp.com/vault/identity-access-management/vault-agent-aws 
Dynamic AWS Secret(User): https://learn.hashicorp.com/vault/getting-started/dynamic-secrets
Katakoda Interactive: https://www.katacoda.com/?q=vault&hPP=12&idx=scenarios&p=0&is_v=1
