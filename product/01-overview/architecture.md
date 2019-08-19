[title]: # (Architecture and Security)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1110)

# Architecture and Security

DevOps Secrets Vault operates through two main components:

* on the customer premises: locally installed, OS-specific Command Line Interface executables on workstations used to operate DevOps Secrets Vault

* in the cloud: a secrets vault created as a tenant per DSV as a multi-tenancy SaaS delivered by an AWS instance

DSV supports the Thycotic One, AWS, and Azure cloud authentication providers.

Activities originate on customer premises in three ways:

* a command entered manually using the CLI
* a command issued by a running shell script or application
* an API call by an application
  
---
  
**Architectural Summary View: DevOps Secrets Vault**
![Image](./images/dsv-architecture-simple-01.png)
  
The API Gateway receives API calls, obtains the responses, and relays them to the caller using HTTP GET, PUT, POST and other methods common to the REST architectural style. The Authorizer uses OAuth to handle API Gateway authorization.

The Vault Application hosts the core DSV functionality, essentially a set of AWS Lambda (serverless) commands. Lambda auto-scales to demand.

Extensive logging enables strong audit trails and protections, while encryption protects secrets in the vault and anywhere data is at rest.

## Availability

Th DSV Uptime SLA percentage is 99.90 percent based on the uptimes of component
products and services.

## Business Continuity

AWS Dynamo databases feature point in time recovery backups with a Recovery
Point Objective (RPO) of 5 minutes. The recovery time objective (RTO) is under 6
hours with a target of 20 minutes.

## Disaster Recovery

For the DSV application, the broadest scale of consequence for a disaster would
be equivalent to an AWS region failing. That would cause complete loss of access
to Thycotic’s cloud entities located in the failed region for an indefinite,
ongoing time.

In such a circumstance, Thycotic would rebuild using AMI/Cloud formation files
and deploy in a new region.

## Confidentiality

In discussing confidentiality, we must interpret the term against the different
states in which data exists.

### Data at Rest

Information *about* customers in DynamoDB, and application activity and related logs stored in S3 and sometimes in Elasticsearch during analysis, will always be encrypted when at rest via AWS KMS. If the hardware for those resources were stolen, no breach would occur, since the data is encrypted.

Customer secret data is further encrypted by the application with a customer specific key managed by Thycotic. This helps ensure that if data were exposed, either via a breach in the Amazon Web Services APIs or an application vulnerability that granted read access to a tenant database, the secret data would remain encrypted.

### Data in Transit

DSV establishes the https connection using the TLS 1.2 protocols. For
server-side authentication, DSV relies on Amazon-issued digital certificates.

## Client Authentication

DSV provides five methods for client authentication:

* username/password

* Thycotic One

* Client ID

* AWS IAM

* Microsoft MSI

Authentication grants an access token with a one-hour time-to-live (TTL). When
the token times out, DSV requires re-authentication.

The username/password authentication method uses a refresh token good for 48
hours. The refresh token renews along with each new access token, so the 48
hours counts relative to the last access token’s time of issuance. If the
refresh token expires, DSV requires re-authentication.

Username/password could also link to Thycotic1 for authentication. This allows
the initial user to reset the password if needed.

## Integrity Checks


### CLI Code Signing

The download website provides a 256-bit hash of the executable files in a text file, so that customers may run a hash check on the downloaded material. The Windows CLI executable is also signed.

### Token Signing

Access tokens granted to users or applications must transit from the client to
the API, potentially allowing an unauthorized party to tamper with the tokens.
To prevent this, DSV signs access tokens.

## Personally Identifiable Information (PII) and GDPR

DSV requires certain personally identifiable information (PII) to identify each
user’s account. This includes the user’s name, email address, and password,
these being the minimum necessary for authentication, and the user’s IP address,
used during auditing as an indicator of the user’s location.

DSV functions to store and protect user’s “secrets,” and to make the secrets
accessible to the user and potentially their designees. The term secrets here
commonly means passwords, which are not PII, but DSV users can store anything
they choose as a secret—for example, images, documents, or other files.

* Accordingly, only users know whether DSV secrets have PII status.

* Because the nature of DSV is to encrypt and protect secrets for users, secrets that are PII will de facto benefit from DSV’s stringent controls for privacy and user control, in accordance with both the letter and spirit of the GDPR.

Only select, trusted employees of Thycotic can access secrets data and decrypt
it, and only via a controlled process that generates an audit trail inaccessible
to those employees. This serves the interests of users without compromising
their privacy and control.

In GDPR terms, Thycotic customers are the data controllers, and Thycotic is the
data processor.

* The customer determines all information (the secrets) stored in the vault and decides how long to store it.

* Each DSV customer entirely controls their users, their user roles, and the access to secrets by their users, according to the policies of the customer organization. DSV logs activity so the customer can monitor access and changes to the secrets, users, and roles within the vault—again, all according to the customer’s policies.

* For traceability, DSV logs include source IP addresses and time stamps.

Thycotic conducts a Privacy Impact Assessment (PIA) annually to verify continued
conformance to GDPR principles.

### Third Party GDPR Conformance Assessment 

The Thycotic SOC 2 Type II report contains an independent third-party assessment of our control environment for conformance to applicable GDPR criteria.

The report ties to the AICPA’s Trust Services Criteria (specifically the Security, Availability, and Confidentiality criteria) and issues annually in accordance with the AICPA’s AT Section 101 (Attest Engagements).


