# Certificate for Reverse Proxy using HashiCorp Vault

## Table of Contents

- [Certificate for Reverse Proxy using HashiCorp Vault](#certificate-for-reverse-proxy-using-hashicorp-vault)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Scope](#scope)
  - [Implementing Certificate on NGINX Reverse Proxy](#implementing-certificate-on-nginx-reverse-proxy)

## Changelog

| Version | Date       | Description                | Author          |
| ------- | ---------- | ------------------------   | --------------- |
| 0.1     | 23.07.2025 | Draft version creation     | Rachel Beulah   |

## Introduction

### Purpose

To guide the DHC Operations team in creating and using SSL certificates for NGINX reverse proxy using Vault's PKI.

### Scope

To provide detailed work instructions for the Operations (OPS) team on issuing and implementing a public or customer-issued certificate for Reverse proxy using HashiCorp Vault's PKI Secrets Engine.

## Implementing Certificate on NGINX Reverse Proxy

This section details the general steps to issue certificate from Vault and deploy it on an NGINX Reverse Proxy.

**Step 1: Access Vault UI and Select Certificate Role**

- Login to the Vault UI, navigate to the relevant PKI Secrets Engine, then go to Roles and select the appropriate role (e.g., nginx-cert, web-server-role) used for issuing NGINX certificates.

 > Note: Detailed instructions on enabling the PKI engine, creating roles, and setting permissions are available in the separate document titled *“Certificate Management using HashiCorp Vault PKI”.* (```wiCertificatesUsingVaultPKI.md```)

**Step 2: Generate Certificate**

- Within the selected role's view, navigate to the "Generate Certificate" tab.
- Enter Common Name: In the "Common Name" field, enter the primary hostname for the reverse proxy (e.g., service.domain.com).
- Add SANs, TTL:
  - Subject Alternative Names (SANs): If the certificate needs to cover additional hostnames (e.g., another-service.domain.com), enter them in the "Subject Alternative Names" field, separated by commas.
  - TTL (Time-To-Live): Can specify a TTL for this specific certificate issuance. This value cannot exceed the max_ttl defined in the role. If left blank, it will use the role's default TTL.
- Click the "Generate" button.
- Vault will generate and display the certificate, its private key, and the issuing CA certificate.

**Step 3: Download and Prepare the Certificate Files**

- View Generated Certificate Details: After clicking "Generate", the Vault UI will display the issued certificate's details.
  - Certificate (PEM Format): Server's public certificate.
  - Private key (PEM Format): Corresponding private key.
  - CA Chain (PEM Format): This includes the certificate of the CA that signed the server certificate, and any intermediate CAs up to the root.
  - Issuing CA (PEM Format): This is specifically the certificate of the immediate CA that signed the server certificate.

- Download the Certificate: Locate and click the "Download" button. It will download a .pem file that only contains the server certificate. Save this file as nginx_service.crt (or .pem).
- Manually Copy the Private Key: Since the "Download" button does not include the private key in the downloaded .pem file, you must manually copy the content from the "Private key" section displayed directly in the Vault UI. Save this copied content into a new file named nginx_service.key (or .pem).
- Manually Copy the Issuing CA Certificate: Similarly, manually copy the content from the "Issuing CA" section displayed in the Vault UI. Save this copied content into a new file named issuing_ca.crt (or .pem).

> Note: This private key is a one-time view. Once you navigate away from this page or refresh it, the private key will no longer be displayed.
> Security Note: The private key (nginx_service.key) is highly sensitive. Handle it with extreme care and ensure it is stored securely on the NGINX server with restricted permissions immediately after copying.

**Step 4: Configure NGINX Reverse Proxy**

- Transfer Files to NGINX Server:
  - Securely transfer nginx_service.crt, nginx_service.key to the NGINX server.
  - A recommended location is /etc/nginx/ssl/cert/. Create the directory if it doesn't exist.
  - Set appropriate permissions to protect the private key.

- Update NGINX Configuration File:
  - Edit the NGINX server block configuration file (e.g., /etc/nginx/conf.d/reverseproxy.conf).

```bash
server {
    listen 443 ssl;
    server_name reverseproxy-service.domain.com; # Common Name/Domain

    # Paths to your certificate and key files
    ssl_certificate /etc/nginx/ssl/cert/nginx_service.crt;
    ssl_certificate_key /etc/nginx/ssl/cert/nginx_service.key;

    location / {
        proxy_pass http://backend_service_ip:port/; # actual backend service
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

- Test NGINX Configuration:

```bash
sudo nginx -t
```

Ensure the output shows syntax is ok and test is successful.

- Reload NGINX:

```bash
sudo systemctl reload nginx
```

- Verify Certificate Installation:
  - Access `https://reverseproxy-service.domain.com` in web browser. Verify that the connection is secure and the certificate details match the one you just issued.
  - Use below command from a client machine to inspect the certificate chain and ensure it's presented correctly.

  ```bash
  openssl s_client -connect reverseproxy-service.domain.com:443 -showcerts 
  ```

> **Important Note:** For browsers and applications on client machines to fully trust the certificate issued by the Vault's PKI, the Root CA certificate from the Vault's PKI must be installed in the trusted certificate store of those client machines. If this Root CA certificate is not installed, browsers will display a "Not Secure" or "Untrusted Certificate" warning, even if the certificate is correctly configured on the NGINX reverse proxy.
