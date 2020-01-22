[title]: # (Architecture and Security)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1110)

# Architecture and Security

DevOps Secrets Vault operates through two main components:

* on the customer premises: locally installed, OS-specific Command Line Interface executables on workstations used to operate DevOps Secrets Vault
* in the Cloud: a Secrets vault created as a tenant, in keeping with DSV as a multi-tenancy SaaS delivered by redundant AWS instances

DSV supports the Thycotic One, AWS, and Azure Cloud authentication providers.

Activities originate on customer premises in three ways:

* a command entered manually using the CLI
* a command issued by a running shell script or application
* an API call by an application

**Architectural Summary View: DevOps Secrets Vault**

![Architectural Summary View of DevOps Secrets Vault](./images/ha-dr-architecture-scaled.png "Architectural Summary View of DevOps Secrets Vault")

![](./images/spacer.png)

DevOps Secrets Vault leverages **AWS DynamoDB** global tables for data storage, with a configuration using automatic dual-region replication as a continuous backup mechanism.

* Of the two AWS Regions used in this architecture, one serves as the primary application platform and the other as a hot stand-by.
* Thycotic monitors both regions via **AWS Route 53** so that if the primary platform fails, client traffic automatically routes to the hot stand-by in under one minute.

Users authenticate by a ThycoticOne, Amazon AWS, or Microsoft Azure authentication provider. Within the DSV application platform, the API Gateway receives API calls, obtains the responses, and relays them to the caller using HTTP GET, PUT, POST and other methods common to the REST architectural style. The Authorizer uses OAuth to handle API Gateway authorization.

The Vault Application hosts the core DSV functionality, essentially a set of AWS Lambda serverless commands. Lambda auto-scales to demand.

Extensive logging enables strong audit trails and protections, while encryption protects Secrets in the vault and anywhere data is at rest.

## Availability

Thycotic architected DSV to support 99.999% uptime. Consult your EULA (End User License Agreement) for details.

## Business Continuity and Disaster Recovery

For the DSV application, the broadest scale of consequence for a disaster would be equivalent to an AWS region failing. The dual-region architecture mitigates this risk, providing switch-over to the hot stand-by within one minute should the primary platform become impaired for any reason, whether a disaster or other cause.

## Confidentiality

In discussing confidentiality, we must interpret the term against the different states in which data exists.

### Data at Rest

Information *about* customers in DynamoDB, and application activity and related logs stored in S3 and sometimes in Elasticsearch during analysis, will always be encrypted when at rest via AWS KMS. If the hardware for those resources were stolen, no breach would occur, since the data is encrypted.

Customer Secret data is further encrypted by the application with a customer specific key managed by Thycotic. This helps ensure that if data were exposed, either via a breach in the Amazon Web Services APIs or an application vulnerability that granted read access to a tenant database, the Secret data would remain encrypted.

### Data in Transit

DSV establishes the https connection using the TLS 1.2 protocols. For server-side authentication, DSV relies on Amazon-issued digital certificates.

## Client Authentication

DSV provides five methods for client authentication:

* username/password
* Thycotic One
* Client ID
* AWS IAM
* Microsoft MSI

Authentication grants an access token with a one-hour time-to-live (TTL). When the token times out, DSV requires re-authentication.

The username/password authentication method uses a refresh token good for 48 hours. The refresh token renews along with each new access token, so the 48 hours counts relative to the last access token’s time of issuance. If the refresh token expires, DSV requires re-authentication.

Username/password could also link to Thycotic1 for authentication. This allows the initial User to reset the password if needed.

## Integrity Checks

Both code signing and token signing further secure the DSV service.

### CLI Code Signing

The download website provides a 256-bit hash of the executable files in a text file, so that customers may run a hash check on the downloaded material. The Windows CLI executable is also signed.

### Token Signing

Access tokens granted to Users or applications must transit from the client to the API, potentially allowing an unauthorized party to tamper with the tokens. To prevent this, DSV signs access tokens.

## Personally Identifiable Information (PII) and GDPR

DSV requires certain personally identifiable information (PII) to identify each User’s account. This includes the User’s name, email address, and password, these being the minimum necessary for authentication, and the User’s IP address, used during auditing as an indicator of the User’s location.

DSV functions to store and protect User’s “Secrets,” and to make the Secrets accessible to the User and potentially their designees. The term Secrets here commonly means passwords, which are not PII, but DSV Users can store anything they choose as a Secret—for example, images, documents, or other files.

* Accordingly, only Users know whether DSV Secrets have PII status.

* Because the nature of DSV is to encrypt and protect Secrets for Users, Secrets that are PII will de facto benefit from DSV’s stringent controls for privacy and user control, in accordance with both the letter and spirit of the GDPR.

Only select, trusted employees of Thycotic can access Secrets data and decrypt it, and only via a controlled process that generates an audit trail inaccessible to those employees. This serves the interests of users without compromising their privacy and control.

In GDPR terms, Thycotic customers are the data controllers, and Thycotic is the data processor.

* The customer determines all information (the Secrets) stored in the vault and decides how long to store it.

* Each DSV customer entirely controls their Users, their User Roles, and the access to Secrets by their Users, according to the policies of the customer organization. DSV logs activity so the customer can monitor access and changes to the Secrets, Users, and Roles within the vault—again, all according to the customer’s policies.

* For traceability, DSV logs include source IP addresses and time stamps.

Thycotic conducts a Privacy Impact Assessment (PIA) annually to verify continued conformance to GDPR principles.

### Third Party GDPR Conformance Assessment 

The Thycotic SOC 2 Type II report contains an independent third-party assessment of our control environment for conformance to applicable GDPR criteria.

The report ties to the AICPA’s Trust Services Criteria (specifically the Security, Availability, and Confidentiality criteria) and issues annually in accordance with the AICPA’s AT Section 101 (Attest Engagements).

![](./images/spacer.png)

![](./images/spacer.png)

  
