# Encryption through AWS KMS

## Encryption with cli
```
export keyID="YOUR_KEY_ID"
echo "my-super-secret-data" > secret.txt
```

```
aws-encryption-cli --encrypt --input secret.txt \
	--master-keys key=$keyID \
	--encryption-context purpose=demo \
	--metadata-output ~/kms-metadata \
	--output .
```

## Decryption with cli
```
aws-encryption-cli --decrypt --input secret.txt.encrypted \
	--output myPlaintextData \
	--metadata-output ~/kms-metadata \
	--encryption-context purpose=demo
```
