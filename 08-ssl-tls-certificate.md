# SSL/TLS, Encryption, Certificates, Public and Private Keys — DevOps Foundation Notes

## 1. What Is Encryption?

Encryption converts readable information (plaintext) into unreadable information (ciphertext) using a cryptographic algorithm and key.

Example:

```text
Plaintext:
Hello, Amazon!

        |
        | Encryption + key
        v

Ciphertext:
Unreadable encrypted data

        |
        | Decryption + correct key
        v

Plaintext:
Hello, Amazon!
```

Encryption protects sensitive information from unauthorized access.

Examples:
- HTTPS websites
- Online banking
- API communication
- SSH remote server access
- Database connections
- Secure file transfers

## 2. What Is SSL and TLS?

**SSL** stands for Secure Sockets Layer.

**TLS** stands for Transport Layer Security.

Both are protocols designed to protect communication over a network. TLS is the modern replacement for SSL.

SSL 2.0, SSL 3.0, TLS 1.0, and TLS 1.1 are obsolete and should not be enabled. Modern deployments generally use TLS 1.2 or TLS 1.3.

### Why do we need TLS?

Imagine you access an online banking website over an unencrypted HTTP connection.

```text
Browser ----------------------> Server
        HTTP: unencrypted
```

Someone able to observe or manipulate the network traffic may be able to read or modify the communication.

With HTTPS:

```text
Browser ======================> Server
          TLS encrypted
```

TLS provides three major security properties:

1. **Confidentiality:** Protects data from being read by unauthorized parties.
2. **Integrity:** Helps detect unauthorized modification of data.
3. **Authentication:** Helps the client verify the server's identity.

Client authentication can also be used when required, such as with mutual TLS (mTLS).

## 3. What Is HTTPS?

HTTPS means HTTP Secure. It is HTTP communication protected by TLS.

Example:

```text
https://www.example.com
```

When you visit an HTTPS website:

1. Your browser connects to the server.
2. The server presents its TLS certificate.
3. The browser checks the certificate's identity, validity, and trust chain.
4. The client and server negotiate security parameters and establish shared session keys.
5. Application data is encrypted using symmetric cryptography.

HTTPS commonly uses TCP port `443` with HTTP/1.1 or HTTP/2. HTTP/3 uses QUIC over UDP, with TLS 1.3 integrated into the connection setup.

## 4. What Is Symmetric Encryption?

Symmetric encryption uses the same secret key to encrypt and decrypt data.

Example:

```text
Plaintext
    |
    | Encrypt using secret key
    v
Ciphertext
    |
    | Decrypt using same secret key
    v
Plaintext
```

Both parties must securely obtain the same secret key.

### Example algorithms

- AES-GCM
- ChaCha20-Poly1305
- AES-CCM

AES-GCM and ChaCha20-Poly1305 are authenticated encryption algorithms that provide confidentiality and integrity protection.

### Advantages

- Very fast
- Efficient for large amounts of data
- Suitable for encrypting web traffic, files, and network communication

### Disadvantage

The parties need a secure way to establish or exchange the secret key.

### Where is it used?

TLS uses symmetric encryption to protect the actual application data after establishing the connection's traffic keys.

## 5. What Is Asymmetric Encryption?

Asymmetric cryptography uses a pair of mathematically related keys:

- Public key
- Private key

The public key can be shared. The private key must remain secret.

```text
             KEY PAIR
                |
        +-------+-------+
        |               |
        v               v
    Public key      Private key
    Can share       Keep secret
```

Asymmetric cryptography supports several operations, including digital signatures and key establishment.

### Example: Digital signatures

A sender signs data using their private signing key.

The receiver verifies the signature using the corresponding public key.

```text
Data + Private Signing Key
            |
            v
      Digital Signature
            |
            v
 Receiver verifies signature
 using the Public Key
```

A valid signature helps establish that the data was signed by the holder of the private key and has not been modified since signing, assuming the algorithm and keys are secure.

### Important distinction

For certain asymmetric encryption algorithms, data encrypted using a public key can be decrypted using the corresponding private key.

However, **modern TLS does not simply encrypt all website traffic with the server's public key**. Modern TLS generally uses ephemeral key exchange, such as ECDHE, to derive shared session secrets. Digital signatures help authenticate the handshake.

## 6. Symmetric vs. Asymmetric Encryption

| Feature | Symmetric | Asymmetric |
|---|---|---|
| Keys used | Shared secret key | Public/private key pair |
| Performance | Generally very fast | Generally more computationally expensive |
| Key distribution | Shared secret must be established securely | Public key can be distributed openly; private key remains secret |
| Common algorithms | AES-GCM, ChaCha20-Poly1305 | RSA, ECDSA, Ed25519, ECDHE |
| Common purpose | Encrypting application data | Signatures, authentication, key establishment |
| TLS role | Protects application traffic | Authenticates the server and helps establish shared secrets |

**Remember:** TLS combines cryptographic techniques rather than relying on only one encryption method.

## 7. What Is a Public Key and a Private Key?

### Public key

The public key may be shared with other systems.

Uses include:
- Verifying digital signatures
- Supporting certain public-key encryption schemes
- Participating in key-establishment protocols

### Private key

The private key must remain secret and be protected from unauthorized access.

Uses include:
- Creating digital signatures
- Decrypting data in schemes that support public-key encryption
- Participating in private-key operations for key establishment

Example:

```text
server.key  = Private key
server.crt  = Certificate containing the server's public key
```

The certificate contains the public key and identity information, along with a digital signature from a certificate issuer. It does not contain the server's private key.

**Never upload a private key to GitHub or share it publicly.**

## 8. What Is SSH?

SSH stands for Secure Shell. It is a protocol for securely connecting to remote computers and running commands.

Example:

```bash
ssh thor@192.168.1.20
```

SSH commonly uses TCP port `22`.

SSH keys can provide public-key authentication:

```text
Your laptop                         Remote server
-----------                         -------------
Private key                         Public key
Keep secret                         Stored in authorized_keys
     |                                    |
     +---- Authentication protocol ------+
```

The server uses the authorized public key to verify that the client can prove possession of the corresponding private key.

SSH key authentication and TLS certificates are related cryptographic concepts, but they are not interchangeable credentials. They use different protocols and formats.

## 9. Generate SSH Keys Using ssh-keygen

### Step 1: Generate an Ed25519 SSH key pair

```bash
ssh-keygen -t ed25519 -C "devops-laptop"
```

Follow the prompts to choose a file location and, preferably, a passphrase.

Typical output files:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

- `id_ed25519`: Private key
- `id_ed25519.pub`: Public key

### Step 2: View the public key

```bash
cat ~/.ssh/id_ed25519.pub
```

It is generally safe to share this public key with the intended server administrator.

### Step 3: Copy the public key to a server

Where `ssh-copy-id` is available and password-based setup is permitted:

```bash
ssh-copy-id thor@192.168.1.20
```

Then connect:

```bash
ssh thor@192.168.1.20
```

The server must be configured to accept the key, and your private key must remain on your trusted client device.

### Step 4: Protect the private key

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

These are typical Linux permissions for a personal SSH key pair.

### Important

`ssh-keygen` is primarily used for SSH keys. For TLS server certificates, use OpenSSL or a certificate-management tool.

## 10. What Is OpenSSL?

OpenSSL is a toolkit and cryptographic library used for tasks such as:

- Generating cryptographic keys
- Creating certificate signing requests (CSRs)
- Creating test certificates
- Inspecting certificates
- Testing TLS connections
- Converting supported certificate and key formats

Check the installed version:

```bash
openssl version
```

OpenSSL is useful for DevOps engineers who work with HTTPS, Nginx, Apache, certificates, and TLS troubleshooting.

## 11. Generate a Private Key Using OpenSSL

### Option A: Generate an RSA private key

```bash
openssl genrsa -out server.key 2048
```

This generates a 2048-bit RSA private key.

Protect the file:

```bash
chmod 600 server.key
```

RSA can be used for digital signatures and, in supported schemes, public-key encryption. Modern TLS typically uses RSA certificates for signatures rather than encrypting the entire traffic or session secret directly with the RSA key.

### Option B: Generate an elliptic-curve private key

For example, using the P-256 curve:

```bash
openssl genpkey \
  -algorithm EC \
  -pkeyopt ec_paramgen_curve:P-256 \
  -out server.key
```

This creates an EC private key suitable for supported cryptographic operations.

### Inspect the key

For an RSA key:

```bash
openssl rsa -in server.key -check -noout
```

For a generic private key:

```bash
openssl pkey -in server.key -noout -text
```

Be careful when printing key details to terminal logs or sharing screenshots. Never expose private key material.

## 12. What Is a Digital Certificate?

A digital certificate binds an identity, such as a domain name, to a public key.

A typical TLS server certificate includes:
- Subject and/or Subject Alternative Names (SANs)
- Public key
- Issuer information
- Validity dates
- Key usage information
- Digital signature from the issuer

Example:

```text
Certificate
    |
    |-- Domain identity: example.com
    |-- Public key
    |-- Issuer: Certificate Authority
    |-- Valid from / expiration date
    |-- Digital signature
```

A certificate is not the same thing as a private key.

Typical files:

```text
server.key   Private key
server.csr   Certificate signing request
server.crt   Server certificate
ca-bundle.crt  Issuing/intermediate certificates, as needed
```

File extensions are conventions. The actual file format depends on the encoding and contents.

## 13. What Is a Certificate Authority (CA)?

A Certificate Authority is an organization or service that issues digitally signed certificates under its certificate-issuance policies.

Examples of public CAs include:
- Let's Encrypt
- DigiCert
- GlobalSign
- Sectigo

A browser or operating system maintains trusted root certificates. A server normally supplies its leaf certificate and the necessary intermediate certificates, allowing the client to build a chain to a trusted root.

### How does a CA establish trust?

```text
Trusted Root CA
       |
       v
Intermediate CA
       |
       v
Server Certificate
       |
       v
example.com
```

This is a simplified chain. Some chains may omit an intermediate level.

When the browser visits `https://example.com`, it checks whether:
- The certificate is valid for `example.com`
- The certificate is within its validity period
- The certificate chain leads to a trusted root
- The signatures and constraints are valid
- The certificate is suitable for the intended use

The browser does not simply trust a certificate because it exists. It validates the identity and trust chain.

## 14. Self-Signed Certificate vs. CA-Signed Certificate

### Self-signed certificate

A certificate signed using its own private key rather than issued under a trusted CA hierarchy.

Common uses:
- Local development
- Temporary testing
- Controlled internal environments where trust is configured explicitly

Browsers generally do not trust an arbitrary self-signed website certificate by default.

### CA-signed certificate

A certificate issued and signed by a CA whose chain the client trusts.

Common uses:
- Public HTTPS websites
- Production APIs
- Customer-facing services

| Feature | Self-signed | CA-signed |
|---|---|---|
| Issuer | Typically the same entity as the certificate subject | Certificate authority |
| Default browser trust | Usually not trusted automatically | Trusted when the chain and identity validate |
| Typical use | Testing or managed private environments | Public production services |
| Requires private key protection | Yes | Yes |

A self-signed certificate can encrypt traffic, but encryption alone does not establish that the server is the intended one. Trust and identity validation matter.

## 15. Create a Certificate Signing Request (CSR)

A CSR contains the public key and identity information that you want a CA to certify. It is signed using the corresponding private key.

First, generate the private key:

```bash
openssl genrsa -out server.key 2048
```

Then create the CSR:

```bash
openssl req -new \
  -key server.key \
  -out server.csr \
  -subj "/CN=example.com" \
  -addext "subjectAltName=DNS:example.com,DNS:www.example.com"
```

This is an example CSR for the domain names `example.com` and `www.example.com`.

For modern TLS server certificates, Subject Alternative Names (SANs) are important. Do not rely only on the Common Name (CN) to identify a domain.

Inspect the CSR:

```bash
openssl req -in server.csr -noout -text
```

The CSR contains the public key and requested identity details, not the private key.

**Important:** A CSR is a request, not an issued certificate. A CA must validate and sign the request before it becomes a CA-issued certificate.

## 16. Create a Self-Signed Certificate for Testing

For a local test, you can create a private key and self-signed certificate in one command:

```bash
openssl req -x509 -newkey rsa:2048 \
  -keyout server.key \
  -out server.crt \
  -days 365 \
  -sha256 \
  -nodes \
  -subj "/CN=localhost" \
  -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"
```

This creates:
- `server.key`: Private key
- `server.crt`: Self-signed certificate

The `-nodes` option tells OpenSSL not to encrypt the generated private key with a passphrase. That can simplify testing but increases the importance of filesystem protection.

Check the certificate:

```bash
openssl x509 -in server.crt -noout -text
```

**Do not treat this as a publicly trusted production certificate.** A public browser will not automatically trust this self-signed certificate unless the trust relationship has been configured explicitly.

## 17. How to Obtain a Certificate Trusted by Browsers

For a public domain, a common option is Let's Encrypt, a public Certificate Authority that provides domain-validated certificates.

### Typical process

1. Register or control a domain name.
2. Point the domain to the intended service.
3. Generate or arrange a private key.
4. Request a certificate using an ACME client.
5. Complete domain validation.
6. Receive the issued certificate and chain.
7. Configure Nginx, Apache, or another TLS endpoint.
8. Renew the certificate before expiration.

A CA validates control of the domain using a supported challenge method. It does not need to know your private key.

### Example using Certbot with Nginx

On a supported Linux system, after installing Certbot and its Nginx integration according to the distribution's instructions:

```bash
sudo certbot --nginx -d example.com -d www.example.com
```

This requires:
- A real domain you control
- DNS records pointing appropriately to your server
- Network access for the chosen validation method
- A supported Nginx setup

Certbot can obtain and configure the certificate and may modify Nginx configuration. Review the changes and verify renewal.

Test the renewal setup:

```bash
sudo certbot renew --dry-run
```

For environments that cannot accept inbound connections on standard validation ports, another supported ACME challenge method, such as DNS-01, may be appropriate.

### Where are the certificate files?

The exact paths depend on the CA and ACME client. With a common Certbot installation, files may be located under:

```text
/etc/letsencrypt/live/example.com/
```

Common file names include:

```text
privkey.pem
cert.pem
chain.pem
fullchain.pem
```

The exact layout may vary.

- `privkey.pem`: Private key; keep secret.
- `cert.pem`: Leaf/server certificate.
- `chain.pem`: Intermediate certificate chain.
- `fullchain.pem`: Leaf certificate followed by intermediate certificates.

Nginx commonly uses the private key and full chain for TLS configuration.

## 18. Configure HTTPS in Nginx

A simplified Nginx HTTPS server block looks like this:

```nginx
server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Here:
- Nginx listens for HTTPS connections on port `443`.
- The certificate and private key are loaded by Nginx.
- Requests are forwarded to the local application on port `3000`.

The application backend should be configured and protected appropriately. The example is a basic starting point, not a complete production security configuration.

Check the Nginx configuration:

```bash
sudo nginx -t
```

Reload Nginx after a successful check:

```bash
sudo systemctl reload nginx
```

## 19. What Happens During a TLS Handshake?

A simplified modern TLS 1.3 handshake:

```text
Browser                              Server
   |                                    |
   |------ ClientHello ---------------->|
   |                                    |
   |<----- ServerHello -----------------|
   |<----- Certificate -----------------|
   |<----- CertificateVerify ------------|
   |<----- Finished --------------------|
   |                                    |
   |------ Finished ------------------->|
   |                                    |
   |====== Encrypted HTTP traffic =======|
```

This diagram is simplified; some messages can be combined or differ according to the negotiated options.

### Explanation

1. **ClientHello:** The client offers supported TLS versions, cryptographic options, and key-share information.
2. **ServerHello:** The server selects compatible options and contributes its key-share information.
3. **Certificate:** The server supplies its certificate chain.
4. **CertificateVerify:** The server proves possession of the private key corresponding to the certificate's public key by signing handshake data.
5. **Finished messages:** Both sides verify the handshake transcript and establish the authenticated connection.
6. **Encrypted application traffic:** The endpoints use derived symmetric traffic keys to protect application data.

The client also validates the certificate chain and domain identity.

TLS 1.3 uses ephemeral key exchange in its standard handshake to establish shared secrets. This supports forward secrecy when appropriate ephemeral key exchanges are used and their private values are discarded.

## 20. How OpenSSL Helps Troubleshoot TLS

### Check a certificate's dates and identity

```bash
openssl x509 -in server.crt -noout -subject -issuer -dates
```

### Check Subject Alternative Names

```bash
openssl x509 -in server.crt -noout -ext subjectAltName
```

### Verify a certificate against a trusted CA bundle

```bash
openssl verify -CAfile ca-bundle.crt server.crt
```

Use an appropriate trusted CA bundle and provide intermediate certificates where required. This command does not, by itself, verify that the certificate is valid for a particular hostname.

### Test a remote TLS endpoint

```bash
openssl s_client -connect example.com:443 -servername example.com
```

The `-servername` option sends Server Name Indication (SNI), which is important when a server hosts multiple TLS domains.

For a concise diagnostic:

```bash
openssl s_client \
  -connect example.com:443 \
  -servername example.com \
  -verify_return_error
```

Certificate verification depends on the trust store available to OpenSSL and the server's presented chain.

### Check an HTTPS endpoint using curl

```bash
curl -Iv https://example.com
```

This helps inspect the TLS handshake, certificate validation, HTTP response, and connection errors.

Do not use `curl -k` or `--insecure` as a permanent solution. Those options disable certificate verification and can hide security problems.

## 21. Common TLS Problems

| Problem | Possible cause |
|---|---|
| Certificate expired | The certificate needs renewal |
| Hostname mismatch | The requested hostname is not covered by the certificate SANs |
| Untrusted issuer | Missing intermediate certificate or untrusted CA |
| Private key mismatch | The private key does not correspond to the certificate |
| Connection refused | Service is stopped or not listening on the target port |
| Connection timeout | Firewall, routing, or network access issue |
| TLS handshake failure | Unsupported protocol/cipher configuration, certificate issue, or other handshake incompatibility |
| Permission denied reading key | Service account cannot access the private key |
| Renewal failure | DNS, HTTP challenge, firewall, or ACME client configuration problem |

## 22. Essential DevOps Security Practices

1. Never share or commit private keys.
2. Protect private-key files with restrictive permissions.
3. Use TLS 1.2 or TLS 1.3 with supported secure configurations.
4. Use a certificate trusted by intended clients.
5. Ensure the certificate covers the actual hostname.
6. Monitor expiration dates and automate renewal.
7. Keep the complete certificate chain configured correctly.
8. Validate certificates rather than disabling verification.
9. Restrict private-key access to authorized processes and administrators.
10. Keep private keys and passwords in suitable secret-management systems.
11. Use SSH keys for SSH authentication and TLS certificates for the relevant TLS use case.
12. Test configuration changes before reloading production services.

## 23. Quick Revision

- **SSL:** Obsolete predecessor to TLS.
- **TLS:** Protocol for secure network communication.
- **HTTPS:** HTTP protected by TLS.
- **Symmetric encryption:** Uses a shared secret key for encryption and decryption.
- **Asymmetric cryptography:** Uses public/private keys for operations such as digital signatures and key establishment.
- **Public key:** Can be shared.
- **Private key:** Must remain secret.
- **SSH:** Secure remote access protocol, commonly on port 22.
- **OpenSSL:** Toolkit for keys, CSRs, certificates, and TLS troubleshooting.
- **CSR:** Certificate Signing Request containing the public key and requested identity information.
- **Certificate:** Binds an identity to a public key and carries an issuer's signature.
- **CA:** Certificate Authority that issues certificates under its policies.
- **Self-signed certificate:** Signed by its own key; not trusted by browsers by default.
- **CA-signed certificate:** Can be trusted when its chain and identity validate.
- **TLS handshake:** Negotiates security parameters, authenticates the server, and establishes traffic keys.
- **Let's Encrypt:** Public CA that issues certificates using domain validation.
- **Port 443:** Common HTTPS port.

## Learning Log

**Topic:** SSL/TLS, Encryption, Cryptographic Keys, SSH, OpenSSL, and Certificates

**What I learned:**
- Difference between symmetric and asymmetric cryptography.
- How public and private keys work.
- Difference between SSH keys and TLS certificates.
- How to generate SSH keys with `ssh-keygen`.
- How to generate RSA private keys using `openssl genrsa`.
- How to create and inspect a CSR.
- Difference between a self-signed certificate and a CA-issued certificate.
- How certificate chains establish trust.
- How to obtain a trusted certificate using an ACME client such as Certbot.
- How HTTPS uses TLS and symmetric traffic encryption.
- How to configure HTTPS in Nginx.
- How to inspect certificates and troubleshoot TLS connections with OpenSSL.

**Suggested GitHub filename:** `ssl-tls-encryption-certificates.md`

**Practical exercises:**
- [ ] Generate an SSH key pair.
- [ ] Generate a TLS private key with OpenSSL.
- [ ] Create and inspect a CSR.
- [ ] Create a self-signed certificate for localhost testing.
- [ ] Inspect a certificate's issuer, dates, and SANs.
- [ ] Test an HTTPS connection with `openssl s_client`.
- [ ] Configure a test Nginx site for HTTPS.
- [ ] Obtain a trusted certificate for a domain you control.
- [ ] Verify certificate renewal and monitor expiration.

**DevOps relevance:** TLS and certificates are essential for securing web applications, API endpoints, reverse proxies, load balancers, Kubernetes ingress, service-to-service communication, and production infrastructure.