[title]: # (Certificate Authority)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6700)

# Certificate Issuance
DevOps Secrets Vault provides the ability to generate and sign leaf (end-entity) certificates or to create and sign a certificate from a certificate signing request(CSR).

A signing certificate is required and it may be generated in DSV or imported from an outside Certificate Authority (CA).  This documentation will often refer to the signing certificate as the "root" certificate.  However, in the case of a signing certificate being imported from an outside CA, best practices would be to use an intermediate certificate as the DSV signing certificate. 

## Generate a Signing Certificate

The command to generate a self-signed root certificate and private key is ```thy pki generate-root```

| Flag             | Description                                                         |
| --------------            | ------------------------------                                      |
| common-name                | Required - The domain name of the root CA|
| rootcapath                 | Required - Path and name of a secret that will contain the signing certificate               |
| domains                    | Required - List of domains that this signing certificate is allowed to sign leaf certificates|
| maxttl                     | Required - Maximum time to live in hours for a leaf cert signed with this signing certificate|
| country                    | Optional|
| state                      | Optional|
| locality                   | Optional|
| email                      | Optional|
| organization               | Optional|

```bash 
pki generate-root --rootcapath myroot --domains google.org,golang.org --common-name thycotic.com --organization Thycotic --country US --state DC --locality Washington --maxttl 1000```

## Register (Import) a Signing Certificate 

The command to register a self-signed root certificate and private key is ```thy pki register```

| Flag                      | Description                                                                   |
| --------------            | ------------------------------                                                |
| certpath                  | Required - Path to a PEM file containing the signing certificate              |
| privkeypath               | Required - Path to a PEM file containing the signing certificate private key  |
| rootcapath                | Required - Path and name of a secret that will contain the signing certificate|
| domains                   | Required - List of domains that this signing certificate is allowed to sign leaf certificates|
| maxttl                    | Required - Maximum time to live in hours for a leaf cert signed with this signing certificate|

As an example, create a file with this certificate and name it cert.pem

```PEM
Begin certificate-----

```
Create a file with this corresponding private key and name it key.pem
```PEM
Begin certificate-----

```

```bash pki register --certpath @cert.pem --privkeypath @key.pem --rootcapath myroot --domains foo.com,bar.com --maxttl 1000```


## Generate and Sign a Leaf Certificate

The command to generate a leaf certificate and private key is ```thy pki leaf```

| Flag             | Description                                                         |
| --------------            | ------------------------------                                      |
| common-name                | Required - The domain name that this certificate will use|
| rootcapath                 | Required - Path and name of a secret that will contain the signing certificate              |
| ttl                        | Optional - Time to live in hours.  If not specified, then the maxttl of the signing certificate will be used|
| rootcapath                 | Optional - Path and name of a secret that will contain this leaf certificate and private key.  If not specified, then DSV will not store the leaf certificate and private key and there will be no way to retrive those after they are generated           |
| country                    | Optional|
| state                      | Optional|
| locality                   | Optional|
| email                      | Optional|
| organization               | Optional|

## Sign a Certificate Given a Certificate Signing Request (CSR)

The command for honoring a certificate signing request is `thy pki sign`

As an example, create a file with this certificate signing request and name it cert.pem

```PEM
Begin CSR-----

```


| Flag                 | Description                                                         |
| --------------            | ------------------------------                                      |
| csrpath                   | Required - Path to a PEM file containing the certificate signing request|
| rootpath                  | Required - Path and name of a secret that will contain the signing certificate              |
| subjectaltnames           | Optional - List of alternative domains 
| ttl                       | Optional - Time to live in hours.  If not specified, then the maxttl of the signing certificate will be used|