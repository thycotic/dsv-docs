[title]: # (Certificate Authority)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6700)

# Certificate Issuance
DevOps Secrets Vault provides the ability to generate and sign leaf (end-entity) certificates or to create and sign a certificate from a certificate signing request (CSR).

All certificates assume **RSA 2048** key-pairs.

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

This command generates a root certificate and corresponding private key for signing leaf certificates with the common name foo.org and/or bar.org.  They are saved in the secret path, /ca/myroot, that is referenced when a leaf certificate is generated and/or signed.

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
| certpath                  | Required - Path to a PEM file containing the signing certificate              |
| privkeypath               | Required - Path to a PEM file containing the signing certificate private key  |
| rootcapath                | Required - Path and name of a secret that will contain the signing certificate|
| domains                   | Required - List of domains that this signing certificate is allowed to sign leaf certificates|
| maxttl                    | Required - Maximum time to live in hours for a leaf cert signed with this signing certificate.  If this is set further out in time than the expiration date of the certificate that is being registered, then there will be an error.  For example, if this signign certifcate has an expiration date next week, the maxTTL maximium number is 189 hours.|



As an example, create a file with this certificate and name it cert.pem

```PEM
-----BEGIN CERTIFICATE-----
MIIDgjCCAmqgAwIBAgIEMZx5bjANBgkqhkiG9w0BAQsFADBhMQswCQYDVQQGEwJV
UzELMAkGA1UECBMCSUExDjAMBgNVBAcTBUJvb25lMRMwEQYDVQQKEwpGb29CYXIs
SW5jMQkwBwYDVQQLEwAxFTATBgNVBAMTDHRoeWNvdGljLmNvbTAeFw0yMDA0MDky
MDI5NDFaFw0yMDA1MjExMjI5NDFaMGExCzAJBgNVBAYTAlVTMQswCQYDVQQIEwJJ
QTEOMAwGA1UEBxMFQm9vbmUxEzARBgNVBAoTCkZvb0JhcixJbmMxCTAHBgNVBAsT
ADEVMBMGA1UEAxMMdGh5Y290aWMuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
MIIBCgKCAQEAtUR1Jh6xRGQaVt9hoiGoZ7b7rSW97aQaFzk+eDICae8EJ1ibGRBP
TRI1FG/9g1KMLXOR0+p4VHyob8sUXRoKXxvYkkxys8F0hUWDnU1drEUxkdi4Gpau
NlBIihfnZQvkgcKq31hbKiJR0i54oCg68r5V6UF8mZMAihkg2kMvzaI0Q4La6wqZ
9RFQRRRKFB34LzIGghZCJWSRF6P6gIbi3eNrMJEgliGjoQXZ2ejruEDVhxjCoyZ6
vgTt23kgqZsNALqPOBk2FxfYCqnKgwM7Qa3QvgMyQ4xnJI0jMBZUjESB/JdbEZ9y
erHldjRbqRR8kGDlbK0x0dQmc4zTB+NsBQIDAQABo0IwQDAOBgNVHQ8BAf8EBAMC
AoQwHQYDVR0lBBYwFAYIKwYBBQUHAwIGCCsGAQUFBwMBMA8GA1UdEwEB/wQFMAMB
Af8wDQYJKoZIhvcNAQELBQADggEBAApFMahE3QH4t7SH3s3M+VTHbiIhkRuqk5Ue
3+S6bJb/tNrDULNeHY2h0ODjfqb7Ai9DIR277uo/VHtAmsZz5lByN2Ke+7aLWcaS
UhzMEUKzrn/1otOd9KdnUbuq/14EBVe2oKxcY5pwIe6g2JU1nhHc6HCD6bU5TgVh
o3VrRt5P9UK8ik+iICnKNmTIQhlD5ageIyZtRf2CMqw4tNWLG58oMuQ4+r5pcejz
EHb5PzbGop0b75GrAQYmhESgxJuTeb7ZvbMB1lnPvqrYcB7OLGerh68lvx+SZuY6
a66WtFchn1eGw4ZT1w9xVNU9XjFwon4jhoUvTqGI4/g46RUcShg=
-----END CERTIFICATE-----
```
Create a file with this corresponding private key and name it key.pem

```PEM
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAtUR1Jh6xRGQaVt9hoiGoZ7b7rSW97aQaFzk+eDICae8EJ1ib
GRBPTRI1FG/9g1KMLXOR0+p4VHyob8sUXRoKXxvYkkxys8F0hUWDnU1drEUxkdi4
GpauNlBIihfnZQvkgcKq31hbKiJR0i54oCg68r5V6UF8mZMAihkg2kMvzaI0Q4La
6wqZ9RFQRRRKFB34LzIGghZCJWSRF6P6gIbi3eNrMJEgliGjoQXZ2ejruEDVhxjC
oyZ6vgTt23kgqZsNALqPOBk2FxfYCqnKgwM7Qa3QvgMyQ4xnJI0jMBZUjESB/Jdb
EZ9yerHldjRbqRR8kGDlbK0x0dQmc4zTB+NsBQIDAQABAoIBABXbITztanZSk5Jx
8LW51TJcL9BawqHKrZKrRkr7zKq1NQ0BdAH7o3Qpg9jo/+o7o8c/LhAdL1EQjsab
9+KZ5zI8i0poiVP/OWtwTERFNcw1s5pgRSJ/lJXb7EMqSq42VuEGdc/kOWnFJZRw
If89mo32QSmUyc9CmEgOa5WlkDf86Kb2LKflqq5Ai2l+6UTPLj/z9iLhCq7jLTmW
ZK5aqWZRzMCn+THg4GTcgAyitW2gmJ6DPRZWshrRQBvUYhcRcJpJ7qPoxD8jL5r2
eWWE3dk5o2RtnZdTYSOy7J8dS9satJMTBgq7FMnAQGa4Kjba+dCtnV4OhhbWn4tb
GcmR2oECgYEAxWeBzoGzvFrjHiwff+iYXRqopBkGEAwH/P/RS1P0cgn3nbAJw6Nz
DmmRHyC4xEAxNg5jgnfvC/a/TrvW3/Icwhg7L1KHj8zwck8aoX7NdScYQBgl4mMB
sCiZbrgpnUAlu1dTKgpT/5Xg0DEyGPMyVJHfawpjkWJuA7Rz4mb73vECgYEA6xK6
XvTUaspY59eyzhESTa4GsU1LG30E2BoJ0KXztD8NEm2FS2TICblnOQjLhwdiSq92
m6vWz5iTmmxl/0r6pHY/t5FbN8EyxusdgBl0MNDuLs6m4nmNnYzUI9j9Azj85jUO
gZM2eKIs03j0fntmoz43atgWC9Gq/8Ivxm0UxlUCgYAFyg1SgxdEVN4ISn75/1ZI
lLmRZnJ5EgFB+DapIOMwXP54D2uZ4zdCmvH4mbsRdlh7H1znvKC0Fx5xLq0UkEMr
pg5GSwNSwk3i7FL5nYBlCSp65rpls0WfZvFo/9molPMGVX9I4lioTDr1oBu6A5fc
RxLoTruwzdQwI6CqaR7F4QKBgF6mh8w8IFtvZiTTwPcgAJKug5tYV+mViSHKOjF8
4Ieu64CEAKu+xJzFvj5EwE56NqWDyOodYrzr3mLLSrZkZk9aHYW4TVZBwEQ/3vz5
QsN1HLJUGvYNo2vQjIpykE1/4LSAoHqj38bq5cmwZiGXZlhMcNvgbeAMaCHa+omW
2kqRAoGBAI2Ao3vMTz/8rG7HWznUL/L59fpiC+UroQu0qLqGPCND6wi2S/e6AEKR
1hPEbuoSoPn/hLah3G/ulZOm2e7wVztzhnTHmI4XfklCZQexCpP8OpP9JP6GeUU9
LlzZJAcduErNsojWq9naXBfGYPY2wI/9vrCoGPhC1uV1DgTYP6OY
-----END RSA PRIVATE KEY-----

```
This command saves this signing certificate and key at the secret path /ca/registeredroot and enables it to sign leaf certs for foo.com and/or bar.com domains (common name).

```bash
thy pki register --certpath @cert.pem --privkeypath @key.pem --rootcapath /ca/registeredroot --domains foo.com,bar.com --maxttl 900
```

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