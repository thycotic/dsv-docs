# SSH Key Issuance

In addition to allowing users to generate TLS certificates, DSV provides an ability to generate SSH-2 compatible public keys (currently only RSA supported) and SSH-2 certificates.
* Using SSH-2 public keys allows an administrator to place your public key on the server for which you wish to access.  This is usually placed in the user's home directory `~/.ssh/authorized_keys` file.
* Using SSH-2 certificates allows DSV's specific root CA to sign the credentials which can then be used to access any SSH Server where DSV's root CA is trusted.

When users create a regular leaf or root certificate with `thy pki leaf` or `thy pki generate-root`, respectively, DSV
automatically creates and saves an SSH-compatible public key. DSV stores it in secret data for the leaf or root secret.

`thy secret read myleaf`

Among other fields, such as those for TLS private key, certificate, there will be a field for the SSH public key:
 ```json
 "sshPublicKey": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC4nmHvYaqodYKU2..."
```

## Adding an SSH public key to a server
In order to authenticate to a remote server using SSH, users need to provide a regular RSA private key, such as a TLS private key DSV generates. Before doing that, users must ensure the server knows about the public key associated with the private key.

For example, administrators can edit the `.ssh/authorized_keys` file and add the public key to the list of authorized public keys for the user of that server.

### Downloading keys
Below is an example of how to fetch the keys from DSV for use with SSH:

###### Fetching the SSH private key:
```bash
thy secret myleaf -f data.privateKey | base64 -d > leaf.priv
```
###### Fetching the public key in SSH-2 format:
```bash
thy secret myleaf -f data.sshPublicKey > leaf.pub
```

The names of the files are arbitrary.
> **_NOTE:_** The private key must first be base64-decoded.

### Authenticating
Having added the public key to the list of authorized keys, users can authenticate:

`ssh -i /path/to/leaf.priv [user@host]`

This example uses a leaf key, but the workflow is the same with a root key.

## Trusting a group of keys signed by a root key
The previous example works well, but there is a maintenance problem that appears if the number of users who authenticate to one particular host increases. Administrators would then have to update the list of authorized public keys for each new key. Instead, administrators could make the server trust all keys that are signed by a root key, one that is higher in the chain of trust.

Clients can then authenticate using any leaf private key that has been signed by a certain root private key. Setting this up is a two-step process.

### Adding a public root key to the server
1. First, the SSH-compatible root public key must be downloaded and saved:
`thy secret myroot -f data.sshPublicKey > root.pub`

2. A file with the key must be uploaded to the server and placed in the `/etc/ssh/` directory.

3. On the server, edit `/etc/ssh/sshd_config`. The following line appended to the file instructs the SSH daemon service to trust all keys signed by a private key associated with a given public key: `TrustedUserCAKeys /etc/ssh/root.pub`
e.g. `echo "TrustedUserCAKeys /etc/ssh/root.pub" >> /etc/ssh/sshd_config`

4. It is often a good idea to restart the SSH daemon service for changes to be applied immediately:
`sudo /etc/init.d/ssh restart`

### Generating an SSH certificate on the client side
To authenticate with a private key, users need to prove that a given leaf key has indeed been signed by a root private key that is connected with the root public key, which the server trusts. To do this, users need to generate an SSH certificate using the root private key and leaf private key. There is a special command for this:

`thy pki ssh-cert --rootcapath myroot --leafcapath myleaf --principals root,ubuntu --ttl 1000`

All arguments are required:
- `rootcapath` is the path to the root CA secret
- `leafcapath` is the path to the leaf CA secret
- `principals` is a list of one or more principals (user or host names) to be included in a certificate when signing a key
- `ttl` is the amount of time (by default, in hours) for which the certificate will be valid

This will return an SSH-2 signed certificate. DSV saves the certificate in the leaf secret data. Users can copy the certificate and save in a file or download it later:

`thy secret myleaf -f data.sshCertificate > leav.priv-cert.pub`

Now it is possible to try to authenticate. Users use the same `ssh` command and pass the same private key. The SSH certificate is also submitted automatically behind the scenes by `ssh`. The command tries to find the certificate in the same directory where the leaf private key is. For this reason, the certificate file must be named in a certain way:
`[private key]-cert.pub`

If there is a leaf private key file named `leaf.priv`, then the certificate must be named `leaf.priv-cert.pub`.

Then authentication works:

`ssh -i leaf.priv [user@host]`

Another client would just need access to the same root secret. With this root secret and a leaf secret, another user can generate an SSH certificate and use it along with the private key to authenticate. The administrators would not have to do any additional setup on the server.
