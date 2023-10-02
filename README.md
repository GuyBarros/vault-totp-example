# Vault for MFA capabilities

Vault has the ability to be used for MFA capabilities using its [TOTP secret engine](https://developer.hashicorp.com/vault/docs/secrets/totp). in the repo there are examples of using vault as an internal provider, for external provider as a virtual authenticator app and how to check the validity of the generated codes. 

### setup
all the examples below take into consideration that Vault is already setup (or using HCP Vault) and that the enviroment variables to access that vault have been configured.

```bash
export VAULT_ADDR=<YOUR_VAULT_ADDRESS>
export VAULT_NAMESPACE=<YOUR_VAULT_NAMESPACE> #if needed, for HCP Vault its admin by default
export VAULT_TOKEN=<YOUR_VAULT_TOKEN>
```


## Enable the Vault TOTP Secret engine:
```bash
 vault secrets enable totp
```


## Vault as MFA As an Internal Provider
to enable Vault to generate TOTP codes you will need to have an identifies, an issuer and the account name and the update period. 


```bash
vault write totp/keys/guy@hashicorp.com generate=true issuer=Vault account_name=guy@hashicorp.com period=30

Key        Value

---        -----

barcode    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

url        otpauth://totp/Vault:guy@hashicorp.com?algorithm=SHA1&digits=6&issuer=Vault&period=60&secret=AHVZ5H6IDOQA5RM7VRWJIWMIACYQISWQ

```

## add to a third party authenticator app

show the QR code in an html page:

```html
<body>

<img src="data:image/png;base64,<insert the barcode from above here>" />

</body>
```

[example html page](barcode.html)

in your authenticator app, you now should be able to scan the QR code and generate totp codes.



## Vault as a virtual Authenticator app. 

Vault also can be used to generate TOTP code for external providers (something like a virtual Google/Microsoft authenticator)

for this you will need the url from your qr code, using a solution like https://webqr.com/ should give you this.

to save it to vault:



```bash
vault write totp/keys/external-key url="otpauth://totp/Vault:test@test.com?secret=Y64VEVMBTSXCYIWRSHRNDZW62MPGVU2G&issuer=Vault"
```

## generate code

In both cases you can generate a TOTP code from Vault


```bash
vault read totp/code/external-key

Key     Value
---     -----
code    435052

```

```bash
vault read totp/code/guy@hashicorp.com

Key     Value
---     -----
code    301976
```

## Verifing the generated code

There is a direct endpoint to check if the generated code is valid:


```bash
vault write totp/code/guy@hashicorp.com code=435052
Key      Value
---      -----
valid    true
```


## API

reminder that Vault is an API first solution so the above can also be done purely using APIs


### Mount the secret engine
```bash
curl -X POST -H "X-Vault-Request: true" -H "X-Vault-Namespace: $(echo $VAULT_NAMESPACE)/" -H "X-Vault-Token: $(vault print token)" -d '{"type":"totp","description":"","config":{"options":null,"default_lease_ttl":"0s","max_lease_ttl":"0s","force_no_cache":false},"local":false,"seal_wrap":false,"external_entropy_access":false,"options":null}' $(echo $VAULT_ADDR)/v1/sys/mounts/totp  | jq .
```

### configure an identity
```bash
curl -X PUT -H "X-Vault-Request: true" -H "X-Vault-Token: $(vault print token)" -H "X-Vault-Namespace: $(echo $VAULT_NAMESPACE)/" -d '{"account_name":"guy@hashicorp.com","generate":"true","issuer":"Vault","period":"30"}' $(echo $VAULT_ADDR)/v1/totp/keys/guy@hashicorp.com  | jq .

```


### generate code 
```bash
curl -H "X-Vault-Request: true" -H "X-Vault-Namespace: $(echo $VAULT_NAMESPACE)/" -H "X-Vault-Token: $(vault print token)" $(echo $VAULT_ADDR)/v1/totp/code/guy@hashicorp.com  | jq .
```

### check code
```bash
curl -X PUT -H "X-Vault-Request: true" -H "X-Vault-Namespace: $(echo $VAULT_NAMESPACE)/" -H "X-Vault-Token: $(vault print token)" -d '{"code":"046734"}' $(echo $VAULT_ADDR)/v1/totp/code/guy@hashicorp.com | jq .

```