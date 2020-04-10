[title]: # (Certificate Authority)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6700)

# Certificate Issuance
DevOps Secrets Vault provides the ability to generate and sign leaf (end-entity) certificates or to create and sign a certificate from a certificate signing request (CSR).

All certificates assume **RSA 2048** key-pairs and **SHA-256 Hashing**

A signing certificate is required and it may be generated in DSV or imported from an outside Certificate Authority (CA).  This documentation will often refer to the signing certificate as the "root" certificate.  However, in the case of a signing certificate being imported from an outside CA, best practices would be to use an intermediate certificate as the DSV signing certificate. 

## Generate a Signing Certificate

The command to generate a self-signed root certificate and private key is `thy pki generate-root`

| Flag             | Description                                                         |
| --------------            | ------------------------------                                      |
| common-name                | Required - The domain name of the root CA|
| rootcapath                 | Required - Path and name of a secret that will contain the signing certificate               |
| domains                    | Required - List of domains that this signing certificate is allowed to sign leaf certificates|
| maxttl                     | Required - Maximum time to live in hours for a leaf cert signed with this signing certificate.  This also sets the expiration date (time) of this root certificate|
| country                    | Optional|
| state                      | Optional|
| locality                   | Optional|
| email                      | Optional|
| organization               | Optional|

This command generates a root certificate named *foobar.com* and corresponding private key for signing leaf certificates with the common name *foo.org* and/or *bar.org*.  They are saved in the secret path, `/ca/myroot`, that is referenced when a leaf certificate is generated and/or signed.

```bash 
thy pki generate-root --rootcapath /ca/myroot --domains foo.org,bar.org --common-name foobar.com --organization FooBar,Inc --country US --state IA --locality Boone --maxttl 1000
```
The output from the above command only shows the certificate and is base64 encoded.

To retrive the root certificate and private key, run `thy secret read --path /ca/myroot`

```json
{
  "attributes": {
    "type": "CA"
  },
  "created": "2020-04-09T20:29:41Z",
  "createdBy": "users:thy-one:dsvtest9519@mailinator.com",
  "data": {
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURnakNDQW1xZ0F3SUJBZ0lFTVp4NWJqQU5CZ2txaGtpRzl3MEJBUXNGQURCaE1Rc3dDUVlEVlFRR0V3SlYKVXpFTE1Ba0dBMVVFQ0JNQ1NVRXhEakFNQmdOVkJBY1RCVUp2YjI1bE1STXdFUVlEVlFRS0V3cEdiMjlDWVhJcwpTVzVqTVFrd0J3WURWUVFMRXdBeEZUQVRCZ05WQkFNVERIUm9lV052ZEdsakxtTnZiVEFlRncweU1EQTBNRGt5Ck1ESTVOREZhRncweU1EQTFNakV4TWpJNU5ERmFNR0V4Q3pBSkJnTlZCQVlUQWxWVE1Rc3dDUVlEVlFRSUV3SkoKUVRFT01Bd0dBMVVFQnhNRlFtOXZibVV4RXpBUkJnTlZCQW9UQ2tadmIwSmhjaXhKYm1NeENUQUhCZ05WQkFzVApBREVWTUJNR0ExVUVBeE1NZEdoNVkyOTBhV011WTI5dE1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBCk1JSUJDZ0tDQVFFQXRVUjFKaDZ4UkdRYVZ0OWhvaUdvWjdiN3JTVzk3YVFhRnprK2VESUNhZThFSjFpYkdSQlAKVFJJMUZHLzlnMUtNTFhPUjArcDRWSHlvYjhzVVhSb0tYeHZZa2t4eXM4RjBoVVdEblUxZHJFVXhrZGk0R3BhdQpObEJJaWhmblpRdmtnY0txMzFoYktpSlIwaTU0b0NnNjhyNVY2VUY4bVpNQWloa2cya012emFJMFE0TGE2d3FaCjlSRlFSUlJLRkIzNEx6SUdnaFpDSldTUkY2UDZnSWJpM2VOck1KRWdsaUdqb1FYWjJlanJ1RURWaHhqQ295WjYKdmdUdDIza2dxWnNOQUxxUE9CazJGeGZZQ3FuS2d3TTdRYTNRdmdNeVE0eG5KSTBqTUJaVWpFU0IvSmRiRVo5eQplckhsZGpSYnFSUjhrR0RsYksweDBkUW1jNHpUQitOc0JRSURBUUFCbzBJd1FEQU9CZ05WSFE4QkFmOEVCQU1DCkFvUXdIUVlEVlIwbEJCWXdGQVlJS3dZQkJRVUhBd0lHQ0NzR0FRVUZCd01CTUE4R0ExVWRFd0VCL3dRRk1BTUIKQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFBcEZNYWhFM1FINHQ3U0gzczNNK1ZUSGJpSWhrUnVxazVVZQozK1M2YkpiL3ROckRVTE5lSFkyaDBPRGpmcWI3QWk5RElSMjc3dW8vVkh0QW1zWno1bEJ5TjJLZSs3YUxXY2FTClVoek1FVUt6cm4vMW90T2Q5S2RuVWJ1cS8xNEVCVmUyb0t4Y1k1cHdJZTZnMkpVMW5oSGM2SENENmJVNVRnVmgKbzNWclJ0NVA5VUs4aWsraUlDbktObVRJUWhsRDVhZ2VJeVp0UmYyQ01xdzR0TldMRzU4b011UTQrcjVwY2VqegpFSGI1UHpiR29wMGI3NUdyQVFZbWhFU2d4SnVUZWI3WnZiTUIxbG5QdnFyWWNCN09MR2VyaDY4bHZ4K1NadVk2CmE2Nld0RmNobjFlR3c0WlQxdzl4Vk5VOVhqRndvbjRqaG9VdlRxR0k0L2c0NlJVY1NoZz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=",
    "domains": ["foo.org", "bar.org"],
    "maxTTL": 1000,
    "privateKey": "LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBdFVSMUpoNnhSR1FhVnQ5aG9pR29aN2I3clNXOTdhUWFGemsrZURJQ2FlOEVKMWliCkdSQlBUUkkxRkcvOWcxS01MWE9SMCtwNFZIeW9iOHNVWFJvS1h4dllra3h5czhGMGhVV0RuVTFkckVVeGtkaTQKR3BhdU5sQklpaGZuWlF2a2djS3EzMWhiS2lKUjBpNTRvQ2c2OHI1VjZVRjhtWk1BaWhrZzJrTXZ6YUkwUTRMYQo2d3FaOVJGUVJSUktGQjM0THpJR2doWkNKV1NSRjZQNmdJYmkzZU5yTUpFZ2xpR2pvUVhaMmVqcnVFRFZoeGpDCm95WjZ2Z1R0MjNrZ3Fac05BTHFQT0JrMkZ4ZllDcW5LZ3dNN1FhM1F2Z015UTR4bkpJMGpNQlpVakVTQi9KZGIKRVo5eWVySGxkalJicVJSOGtHRGxiSzB4MGRRbWM0elRCK05zQlFJREFRQUJBb0lCQUJYYklUenRhblpTazVKeAo4TFc1MVRKY0w5QmF3cUhLclpLclJrcjd6S3ExTlEwQmRBSDdvM1FwZzlqby8rbzdvOGMvTGhBZEwxRVFqc2FiCjkrS1o1ekk4aTBwb2lWUC9PV3R3VEVSRk5jdzFzNXBnUlNKL2xKWGI3RU1xU3E0MlZ1RUdkYy9rT1duRkpaUncKSWY4OW1vMzJRU21VeWM5Q21FZ09hNVdsa0RmODZLYjJMS2ZscXE1QWkybCs2VVRQTGovejlpTGhDcTdqTFRtVwpaSzVhcVdaUnpNQ24rVEhnNEdUY2dBeWl0VzJnbUo2RFBSWldzaHJSUUJ2VVloY1JjSnBKN3FQb3hEOGpMNXIyCmVXV0UzZGs1bzJSdG5aZFRZU095N0o4ZFM5c2F0Sk1UQmdxN0ZNbkFRR2E0S2piYStkQ3RuVjRPaGhiV240dGIKR2NtUjJvRUNnWUVBeFdlQnpvR3p2RnJqSGl3ZmYraVlYUnFvcEJrR0VBd0gvUC9SUzFQMGNnbjNuYkFKdzZOegpEbW1SSHlDNHhFQXhOZzVqZ25mdkMvYS9UcnZXMy9JY3doZzdMMUtIajh6d2NrOGFvWDdOZFNjWVFCZ2w0bU1CCnNDaVpicmdwblVBbHUxZFRLZ3BULzVYZzBERXlHUE15VkpIZmF3cGprV0p1QTdSejRtYjczdkVDZ1lFQTZ4SzYKWHZUVWFzcFk1OWV5emhFU1RhNEdzVTFMRzMwRTJCb0owS1h6dEQ4TkVtMkZTMlRJQ2Jsbk9Rakxod2RpU3E5MgptNnZXejVpVG1teGwvMHI2cEhZL3Q1RmJOOEV5eHVzZGdCbDBNTkR1THM2bTRubU5uWXpVSTlqOUF6ajg1alVPCmdaTTJlS0lzMDNqMGZudG1vejQzYXRnV0M5R3EvOEl2eG0wVXhsVUNnWUFGeWcxU2d4ZEVWTjRJU243NS8xWkkKbExtUlpuSjVFZ0ZCK0RhcElPTXdYUDU0RDJ1WjR6ZENtdkg0bWJzUmRsaDdIMXpudktDMEZ4NXhMcTBVa0VNcgpwZzVHU3dOU3drM2k3Rkw1bllCbENTcDY1cnBsczBXZlp2Rm8vOW1vbFBNR1ZYOUk0bGlvVERyMW9CdTZBNWZjClJ4TG9UcnV3emRRd0k2Q3FhUjdGNFFLQmdGNm1oOHc4SUZ0dlppVFR3UGNnQUpLdWc1dFlWK21WaVNIS09qRjgKNElldTY0Q0VBS3UreEp6RnZqNUV3RTU2TnFXRHlPb2RZcnpyM21MTFNyWmtaazlhSFlXNFRWWkJ3RVEvM3Z6NQpRc04xSExKVUd2WU5vMnZRaklweWtFMS80TFNBb0hxajM4YnE1Y213WmlHWFpsaE1jTnZnYmVBTWFDSGErb21XCjJrcVJBb0dCQUkyQW8zdk1Uei84ckc3SFd6blVML0w1OWZwaUMrVXJvUXUwcUxxR1BDTkQ2d2kyUy9lNkFFS1IKMWhQRWJ1b1NvUG4vaExhaDNHL3VsWk9tMmU3d1Z6dHpoblRIbUk0WGZrbENaUWV4Q3BQOE9wUDlKUDZHZVVVOQpMbHpaSkFjZHVFck5zb2pXcTluYVhCZkdZUFkyd0kvOXZyQ29HUGhDMXVWMURnVFlQNk9ZCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg=="
  },
  "description": "",
  "id": "90de1c6b-3c85-42cf-9d6a-758b48f1daf5",
  "lastModified": "2020-04-09T20:29:41Z",
  "lastModifiedBy": "users:thy-one:dsvtest9519@mailinator.com",
  "path": "ca:myroot",
  "version": "0"
}
```


## Register (Import) a Signing Certificate 

The command to register a signing certificate and private key generated outside of DevOps Secrets Vault is ```thy pki register```

| Flag                      | Description                                                                   |
| --------------            | ------------------------------                                                |
| certpath                  | Required - Path to a PEM file containing the signing certificate.              |
| privkeypath               | Required - Path to a PEM file containing the signing certificate private key.  |
| rootcapath                | Required - Path and name of a secret that will contain the signing certificate.|
| domains                   | Required - List of domains that this signing certificate is allowed to sign leaf certificates.|
| maxttl                    | Required - Maximum time to live in hours for a leaf cert signed with this signing certificate.  If this is set further out in time than the expiration date of the certificate that is being registered, then there will be an error.  For example, if this signing certifcate has an expiration date next week, the maxTTL maximium number is 189 hours.|



As an example, create a file with this certificate and name it `cert.pem`

```PEM
-----BEGIN CERTIFICATE-----
MIIDnjCCAoagAwIBAgIJAMOhi74h4lRqMA0GCSqGSIb3DQEBCwUAMGQxCzAJBgNV
BAYTAlVTMQswCQYDVQQIDAJJTDEQMA4GA1UEBwwHQ2hpY2FnbzEhMB8GA1UECgwY
SW50ZXJuZXQgV2lkZ2l0cyBQdHkgTHRkMRMwEQYDVQQDDApmb29iYXIub3JnMB4X
DTIwMDQxMDAxMjMyOFoXDTI1MDQwOTAxMjMyOFowZDELMAkGA1UEBhMCVVMxCzAJ
BgNVBAgMAklMMRAwDgYDVQQHDAdDaGljYWdvMSEwHwYDVQQKDBhJbnRlcm5ldCBX
aWRnaXRzIFB0eSBMdGQxEzARBgNVBAMMCmZvb2Jhci5vcmcwggEiMA0GCSqGSIb3
DQEBAQUAA4IBDwAwggEKAoIBAQCxDninSZ/wDyXCcRCAgHdGxP8/YW4sX1OcStjl
qOjVVCGEr0wrLG0rDFb/KxVJ3WVM4lh381ZUT/N6qcRrl2ZPupPh9P9jjU5NkJIS
x2wIsuptRFzuw4nSBoIdDdMun0CDbscEuWUIjEdsC5kj7DPLaN16u6icOxxAH9RW
YzQoV92hsjmIZvHtzpCoVMsUMF7ONbzh54wZgajzMPV0jaGKrqLMnuhLs5O1O+AY
4k03NlfsTSNsOA8a+jjXXG331jmuQPh4UphcmUfMjpEfWw6x/qwSrxKz07k6dDWK
KcmJzqAj/MXA7coOvwj7L39uv/cMVzk/MTeLYW2Jbz7h07CBAgMBAAGjUzBRMB0G
A1UdDgQWBBTRG8SieQc672Onj/fPAQss3eA1pjAfBgNVHSMEGDAWgBTRG8SieQc6
72Onj/fPAQss3eA1pjAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IB
AQCuomjUQVYMGcPz1wzc2GJw57dTONnNyLXUdiOpGOrxhep1veFkCQmgrxAMu7Ky
ZNEoINmkHY1fO0p7hAzKIWpFBSpMwDZg/1vamjE0riJ+JxGWo2C34WZqRJHbunK5
cBmZBeER93L76Pc8k6eC/01cus+hiqs2Mg7Ugg0RsV+fEs6BEL0KQQh+VG+rPq6C
WH9GJr9PiLD+gG6rxOZRrXt6gx1XOoK6REj1W5wMaxeS2+SKOHGPhaRE+z1xXC9z
7Y8j7UnAeE9dikJipfgj48zWskUexW6rxYK7hiz5nX3VCP1XpZp5uFhXmegJ1fmD
Qx0dZF6QQRIK4MNGZ2mg1y3F
-----END CERTIFICATE-----
```
Create a file with this corresponding private key and name it `key.pem`

```PEM
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAsQ54p0mf8A8lwnEQgIB3RsT/P2FuLF9TnErY5ajo1VQhhK9M
KyxtKwxW/ysVSd1lTOJYd/NWVE/zeqnEa5dmT7qT4fT/Y41OTZCSEsdsCLLqbURc
7sOJ0gaCHQ3TLp9Ag27HBLllCIxHbAuZI+wzy2jderuonDscQB/UVmM0KFfdobI5
iGbx7c6QqFTLFDBezjW84eeMGYGo8zD1dI2hiq6izJ7oS7OTtTvgGOJNNzZX7E0j
bDgPGvo411xt99Y5rkD4eFKYXJlHzI6RH1sOsf6sEq8Ss9O5OnQ1iinJic6gI/zF
wO3KDr8I+y9/br/3DFc5PzE3i2FtiW8+4dOwgQIDAQABAoIBAEBCGUXVcadlR/X2
pN+OQDu9+UkeaibOfgDGJUvMbpwlyXhnSoSMvh4Wf2hiUXqaUE6EA0mdVeKJlbsZ
7ACEVQxwkYU7LokJ2rZJ1snb+Hh7vprjabr52oYP+J7kypUsFPTeenpbcrCUgMNU
vkKMUgvrxh3qB3qT9V/MbXrgzCgriHazR27/pPLJALnOAusu7C0XGSa7eJSY6ysO
neKWkWtJiPWa3wTp9LHxeHrkYbEd4cx2G3no1SM4IUDOUjAkHJ2OyShkyn/vXUn9
Aygnlp0s26MIgXgk46AqoR0WIwRYu68FqdXdC16GRmcByALKA5XJ4Hqz9Q8ufoJf
/R9PwjECgYEA5cvcHTX+OCbzgUrtODz3ymHK2q2fSoMGGPPiBHQiqIhaVtprCpMp
6hIy4Vk/D2rHbWj+idMufnvAPjr+qJPRzId0VmRkDyLHGq2WjBv40wc5u4Pw+sa9
YPhQNDmCu4wABvc4lbKueP7OtAcp04nLSk3B9ZLBnOjQNMmDVim2db0CgYEAxT8M
XawhG9LpL7tFtIQsvIxTvYlFimC5+CmnFLjcKD/1jqz8rVJSLCEtPZnh2tDcifxh
yo8UA+/nWHy0tF6JIIhfh+DqUWwWCPxJc5djwM8Zs3TrnawIBYWcl3wUM7X6FLSX
v5unb61XjPYWMU6z64cVaCH20sCUXing9Sh4qBUCgYAOXZUwGkz/M6grYAS+bElN
VJm62/nGTbSW4MAzaRM1l/iVz2e7rIGFSYf2wH6JtzIqa9LlyNbyP0hAW63J2hvW
fm1ObU44CAOMbmen8KO4hY4dY90vwDbclgllimba1KC3zsKx0Q7JL5y6cmwx9j5I
Md47POZvqbpCYoqcW1U1vQKBgQC6oxnUWNdLOJqlK5KdaKPcFPv30DgY48WUZ/VM
yk6nVz3HLzA34DkYwJvKOh1Xq2HCvyjZPeE2iH5jYDysnvcp7WBXdh7BxIBlKDNo
SMt+2Xf8Mpnvq6Q7dV3iiOmktIBZrzgXefVI2sCJBSGirlHYfw1mZxzh9o9tOjs+
PnlMsQKBgAUCVf5yqUGETwkv17I/2Fn+l7Hw3Yv8Ced1WKB6bwoF5Hdllr01LgpF
q10bc+NezxCPQd+dBNBgFbcWpWvYPDfte2u6G94G8OqiOXczwu7Z3iI6puukV4Uy
8Nz6NxjrgibNpB/nui0i36HKAyDWmo57mc7UofPCEieIK/g3DnwG
-----END RSA PRIVATE KEY-----

```
This command saves this signing certificate and key at the secret path `/ca/registeredroot` and enables it to sign leaf certs for *foo.com* and/or *bar.com* domains (common name).

```bash
thy pki register --certpath @cert.pem --privkeypath @key.pem --rootcapath /ca/registeredroot --domains foo.com,bar.com --maxttl 900
```

## Generate and Sign a Leaf Certificate

The command to generate a leaf certificate and private key is ```thy pki leaf```

| Flag             | Description                                                         |
| --------------            | ------------------------------                                      |
| common-name                | Required - The domain name that this certificate will use.  This must match a domain in the signing certificate's list.|
| rootcapath                 | Required - Path and name of a secret that will contain the signing certificate.  It does not matter if the signing certificate was generated by DSV or imported.             |
| ttl                        | Optional - Time to live in hours.  If not specified, then the maxttl of the signing certificate will be used.|
| rootcapath                 | Optional - Path and name of a secret that will contain this leaf certificate and private key.  If not specified, then DSV will not store the leaf certificate and private key and there will be no way to retrive them after the initial stdout is deleted.          |
| country                    | Optional.|
| state                      | Optional.|
| locality                   | Optional.|
| email                      | Optional.|
| organization               | Optional.|


For this example, we will request a leaf certificate for *bar.com* and use the imported signing certificate above stored at `/ca/registeredroot`

`thy pki leaf --rootcapath /ca/registeredroot --common-name bar.com --organization FooBar, Inc --country US --state CA --locality 'San Francisco' --ttl 24`


A signed certificate and private key is returned in base64 encoding
```Bash
{
  "certificate": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURaakNDQWs2Z0F3SUJBZ0lFR1lXNFRUQU5CZ2txaGtpRzl3MEJBUXNGQURCaE1Rc3dDUVlEVlFRR0V3SlYKVXpFTE1Ba0dBMVVFQ0JNQ1NVRXhEakFNQmdOVkJBY1RCVUp2YjI1bE1STXdFUVlEVlFRS0V3cEdiMjlDWVhJcwpTVzVqTVFrd0J3WURWUVFMRXdBeEZUQVRCZ05WQkFNVERIUm9lV052ZEdsakxtTnZiVEFlRncweU1EQTBNVEF3Ck1qSTVNVGhhRncweU1EQTBNVEV3TWpJNU1UaGFNR0F4Q3pBSkJnTlZCQVlUQWxWVE1Rc3dDUVlEVlFRSUV3SkQKUVRFV01CUUdBMVVFQnhNTlUyRnVJRVp5WVc1amFYTmpiekVQTUEwR0ExVUVDaE1HUm05dlFtRnlNUWt3QndZRApWUVFMRXdBeEVEQU9CZ05WQkFNVEIySmhjaTVqYjIwd2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3CmdnRUtBb0lCQVFDdUdNbmlITjM4TjRGTGdBNHlESEZTVWYrekxjREFGUWI1SGZleTNDME5VL3RZeHNrTnNRczkKQUJkZGJyUTBMbjNVWkRNL2hVcUZIR2prSGRkUVROSTJMY2IzRGk4QWdLVU85OHVhOHVpWSttTDZZK2llTE9XegozejVNNnRFOGdFbHNlQUJ4VkFwT29hTGlEZVl4MUxWOUdSUlVoZm1hZ1RFNVF4V3pmdTVKU0wyYVd2M3RreUhMCnpFandiaGFDVHV0d0gxM1NrczN5OUNwZ091MW1qV1N3WmU0cjRGY284KzdMMEUvSDZLcG9zQk1mWTV5N24wbm0KeU5NL2ZKM2d3eCtpSkJKa1o1RnJqRWxnNVIyZUs0aG5QdU1zeGFvY05FSElROGNXa1NTOG0zWnpNRnVjYVdFMQpKNlNTSDQrd0ZXazBZdzA1cTRTZnQreEhGK1VocFdmZkFnTUJBQUdqSnpBbE1BNEdBMVVkRHdFQi93UUVBd0lICmdEQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFOQmdrcWhraUc5dzBCQVFzRkFBT0NBUUVBbzdtTjExRFAKb3c5Y3VtWXJlVzdzUEFSSWxUcHBwMStIY1BNa0JhL0JvZUwrOEdtM3JDZWgyQnM4b09YQXhyVmVWSkZ5K0VNQQpIZjhQSjFHazlMeHNzSDJQazk0OTNGMzJlVGhxUWo0d0RuQzg0TkpJZzlYMlpNSkpDSFBjc0wvVU9kenZraEhLCnkvSHk0bDl5Y0dQdGtudmtURkVkTVdKZ2hOcFgvSkxrTFlQZWthNzFORjFPOEFaMFZVbXJXMDR0YVlDYzZ5UVAKMVlJbXhSd1FLNVJiYWMxSWUxVEI5VWc5Z2dvUnhZOUpFKyt5aFRoMU5SK0tYUTZucWVNbk1SdStxaERONjRxVwpmMzhBU1lOMklqRndnTVBEK3E5R3JOdWl2REYxc05lcDVDeFEzdi83S2dtNDNHTFFhZ3o2T0piblNLbmYrM2llCit3MTQxUXZJT1pDZDRnPT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=",
  "privateKey": "LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBcmhqSjRoemQvRGVCUzRBT01neHhVbEgvc3kzQXdCVUcrUjMzc3R3dERWUDdXTWJKCkRiRUxQUUFYWFc2ME5DNTkxR1F6UDRWS2hSeG81QjNYVUV6U05pM0c5dzR2QUlDbER2ZkxtdkxvbVBwaSttUG8Kbml6bHM5OCtUT3JSUElCSmJIZ0FjVlFLVHFHaTRnM21NZFMxZlJrVVZJWDVtb0V4T1VNVnMzN3VTVWk5bWxyOQo3Wk1oeTh4SThHNFdnazdyY0I5ZDBwTE44dlFxWURydFpvMWtzR1h1SytCWEtQUHV5OUJQeCtpcWFMQVRIMk9jCnU1OUo1c2pUUDN5ZDRNTWZvaVFTWkdlUmE0eEpZT1Vkbml1SVp6N2pMTVdxSERSQnlFUEhGcEVrdkp0MmN6QmIKbkdsaE5TZWtraCtQc0JWcE5HTU5PYXVFbjdmc1J4ZmxJYVZuM3dJREFRQUJBb0lCQUdMVUdZNXRHcXE1aTRFagpnV3R4MnNhRFcrY0lHdm92TlpVbktOeDAxbkpSY1VaVkdmN1d1TzE0NXNxWU5GM0c0cEUyREUyTHllREVYdHJZCkFjbEl3ckFVem5TaXJaWFljVnFNMmh6c3RaTloxK1FSNFJRaG9vZTRPL0tIL2gwZEtoRVVFaFJEUTlLZE9ReWcKSFVPK1h3UlR2MUczK0JoNExFdzRROUp3Uks1K1YwRysyZjlqbjQ0M05BZGRTVWZ1UFRpVXVqelRTaWNGSlBKdwp0a1hYeU01VkpzVjN0VEZ2a3ZkVE43WFVhUDNLQ3ZOdU9XWFUrbG1BS21qc2xXSDBIRUJhS0NvWWVqMyt4ZURtCnFFR1A5bXc2eFZVY0hTalgzT1BHVFJrbnR3bXNkRkQ4Z2ZJYi9RZXpVRGVnV0VvM21xSTJpQ2RLbDUwWURLUWkKSUxzNHY1RUNnWUVBeFdxOEdPMGRCRzBkbGJtTWpEUTE5NnQ0ckhGUjhObHNzOXZ6Wnd0VzZ4Z0c4d0NFWnFhTwpVNUlVeXd4YWxBL0xQVmJTdVNHQm54Sy9FQTYrZVJ2cTlxOU5UcEw5UDBDc3dpVldiMHpWdUNDQlZYRitaR3diCkRKcVB0ZHdlb0dxNVZOaUhFUkVEemRuM0RWMVAxZzFyU09wR3BmT0w5OVpYNU9IcGoraEhob2tDZ1lFQTRjSjgKRWhzdS9jc1ZSTjc0MGxsdzRQTU5HMFUxZ01YaTlJVkZ5dkdtQUIxQ3FGUmpZeUtFTHZqQ1h6UFN2ZTRGczRvZQpRY1UzaUVnU1djeEFFSmJ6VTB5Sit6ZHdITkpJOFJMMzhxcTB6dVJUSG1pc2Y4cnhGZUt2QU80NTE1N2R6WmJHClR6MTMwUTRNc1RKbUxyR2xST0MrMHV5UkRqQm92RUl2V2kwV1lTY0NnWUE0MWdYWlYwcW5YNUxJN0dhZVp0bXkKdUZkQnJrNWMvUHZpdkV4VE9seUh5cDhWanV5UGNSeEF5eW5aVzNFb2QzT1g4VXN4cVlmYitGV3hsYzBZcVFUNAppSGZGUzJSRnRhVUhNQ0MyWW5TVlVpWnFKd2F3ZXI4KzNiREtOdGxLYmU5MWtmRXc1S2tudHJ6OXlBT1lLTHplCmZUUmh5c0JkVmdSd0RPcGxXQVpmb1FLQmdCRmEwQXJjU0JwK2VCNFpQZXQ5c0syNlFYR3RPbFd4NEthSGNEd1AKbzRFeXZxTU9DYTNmUTJZUS9YQXdIYTA0RlB3ZVRBRW1WZ1NGOWRNdFhtZG9FMEIrQzhWaUY1NC9sQmZrSzJkZQpOQlFMZlZCREg2K2JQRGxBZWMrS2dLdlFyS0JYVE50ZWtFMWoxUm55RStUWEJ5dHFVNEVIYW9jNnRYSnpiQXgwCmx0blZBb0dCQUladjU2cGNrbXRoMkJqZzdDdnpja2VxbHhBeUxKWU1aaW5sYjhjTDJ5UmV1NEQ5Wm0yNHdFOGkKV1N6OEwwUmFlK01Idk85bXlrckVubHhDcHd5aFUvL05tUDlENmZGYlh1MWpCb1h4ZUlJRWt1Wk9LdzI5Rm1MMgpFSitKV2MrRkY0cGdpZHBUMCtQL25oc2ZTVGt4TmtZaWdCSzJ1dmVBdTJIU0NtRWNlRjBlCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg=="
}
```

## Sign a Certificate Given a Certificate Signing Request (CSR)

The command for honoring a certificate signing request is `thy pki sign`


>NOTE: The common name for the certificate in the CSR must match a domain in the signing certificate's list.   

| Flag                 | Description                                                         |
| --------------            | ------------------------------                                      |
| csrpath                   | Required - Path to a PEM file containing the certificate signing request.|
| rootpath                  | Required - Path and name of a secret that will contain the signing certificate. It does not matter if the signing certificate was generated by DSV or imported               |
| subjectaltnames           | Optional - List of alternative domains.  They must match a domain in the signing certificate's list.
| ttl                       | Optional - Time to live in hours.  If not specified, then the maxttl of the signing certificate will be used.|

As an example, create a file with this certificate signing request and name it `internalSite.csr`.  It is requesting the common name of *foo.org* so we will sign it with the sample root certificate we generated at the top of this page.

```PEM
-----BEGIN CERTIFICATE REQUEST-----
MIIClDCCAXwCAQAwTzELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAklMMSEwHwYDVQQK
DBhJbnRlcm5ldCBXaWRnaXRzIFB0eSBMdGQxEDAOBgNVBAMMB2Zvby5vcmcwggEi
MA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDcmthlMQcfWwZmKZr1G7aYuTLb
j/hCTIlGEhGDcp0elAEnzWGLdFUsbIMdb7ZlO/SEJLb9cVHGgcf9U67s9+1hqUPY
/xwCbHJ7JYfLHZm3XHT5oA2QUmMNqwZlh/YTwUDUr9NYslTZOUm4y6smzfO5TVOC
Z9SFETi3ZfPsknQQ3EEmPso2yJU0yqxHkgozm2bYOItd1ySEOM4R0JLQEBSgLLo4
QLtxJJZiKKVvuhGZ7SZUcXft4RxBq41uv1YyffWeZYa0b/h7hcb7Gj+pnaI/1PWm
vxdkW6cXnpAmL5k0PXlfQARGkBkUFyF3DQGDfT41UfSHE9qWi0gA6wfhXvCFAgMB
AAGgADANBgkqhkiG9w0BAQsFAAOCAQEAmL2JDxGpKmIU60uMUsQXtylObyyIMW0q
bmmqrfccfxdV/WNLLOrm/8g0Rp/eWwAGkQY8tZJnlN+BPK6yFpx1TYW6z2aPGTUT
TgKnaheDWnpCPLkRJRqEIHYe9B+vFvEJXl1lU7pA4FGIsNV+1R2TTG4nBp8Nx7Ng
LWCFT4m90R39wCxXEJMoUOIii8mfeaFwlZstyb/pAPuQoWYebOMCTHxJsxRsr/w9
PBJsTPM+USH1xTUTtbEgY4SGFG7C+SYluFHj9c5hhH40TPv0NH9cmMHxSsbNKbou
wmq9DFjzRXDVjAMLb2fsbBBpQ7/aT30pJWr9jAX0/FH1Ymg2aIK89w==
-----END CERTIFICATE REQUEST-----
```

`thy pki sign --rootcapath /ca/myroot --csrpath @internalSite.csr --ttl 24`

```
The signed certificate comes back in base64 encoding

```Bash
{
  "certificate": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURZakNDQWtxZ0F3SUJBZ0lFRm1OYmV6QU5CZ2txaGtpRzl3MEJBUXNGQURCaE1Rc3dDUVlEVlFRR0V3SlYKVXpFTE1Ba0dBMVVFQ0JNQ1NVRXhEakFNQmdOVkJBY1RCVUp2YjI1bE1STXdFUVlEVlFRS0V3cEdiMjlDWVhJcwpTVzVqTVFrd0J3WURWUVFMRXdBeEZUQVRCZ05WQkFNVERIUm9lV052ZEdsakxtTnZiVEFlRncweU1EQTBNVEF3Ck1qRTROVGxhRncweU1EQTBNVEV3TWpFNE5UbGFNRTh4Q3pBSkJnTlZCQVlUQWxWVE1Rc3dDUVlEVlFRSUV3SkoKVERFaE1COEdBMVVFQ2hNWVNXNTBaWEp1WlhRZ1YybGtaMmwwY3lCUWRIa2dUSFJrTVJBd0RnWURWUVFERXdkbQpiMjh1YjNKbk1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBTUlJQkNnS0NBUUVBM0pyWVpURUhIMXNHClppbWE5UnUybUxreTI0LzRRa3lKUmhJUmczS2RIcFFCSjgxaGkzUlZMR3lESFcrMlpUdjBoQ1MyL1hGUnhvSEgKL1ZPdTdQZnRZYWxEMlA4Y0FteHlleVdIeXgyWnQxeDArYUFOa0ZKakRhc0daWWYyRThGQTFLL1RXTEpVMlRsSgp1TXVySnMzenVVMVRnbWZVaFJFNHQyWHo3SkowRU54QkpqN0tOc2lWTk1xc1I1SUtNNXRtMkRpTFhkY2toRGpPCkVkQ1MwQkFVb0N5Nk9FQzdjU1NXWWlpbGI3b1JtZTBtVkhGMzdlRWNRYXVOYnI5V01uMzFubVdHdEcvNGU0WEcKK3hvL3FaMmlQOVQxcHI4WFpGdW5GNTZRSmkrWk5EMTVYMEFFUnBBWkZCY2hkdzBCZzMwK05WSDBoeFBhbG90SQpBT3NINFY3d2hRSURBUUFCb3pRd01qQU9CZ05WSFE4QkFmOEVCQU1DQjRBd0V3WURWUjBsQkF3d0NnWUlLd1lCCkJRVUhBd0l3Q3dZRFZSMFJCQVF3QW9JQU1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQkh1b2FwSk05VTVUa0IKcU5Pb0hVMnJ3UmxjOUpRRmc5OTd3Y0UxU0dKbUNKTUd0ZkJMajZRRk80RnFJZGU5Qk90N2o0bnZwQUduYXNmaQpzbzBWa09tK1dyZUpuRXJiL0dMK0RpMExKbGxSZHduYWJtY2NXTFVkNm5EWWxGYjZLdEdmU3dYQWJyTTh5VVZjCmdqdU1odUl5d1ExOHR1UEFTWGFrWjUwU2VyOFd4Q3dUMlgvRDhVaGhXR1Ercno5aFV0ZHpUdU5COUdVb21PaGUKb0lXZGxHVVlpcm9sQS9GQk9nWjZCT2gxVnQ4S3lFN0VLRjZJdU1wM3kvc2szcGVMUmpUL0dIK0JxRW5PNmhzZwpia3NOcTNGSWROYmNlTExlV3dLWW1ZUEdQYWFuSnZ3NnZWN3MzRlQ0TUhUaUFtVTRkbTRkZVAvNzRpZXVvTXlXCnNpZTdESkoxCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
}
```

