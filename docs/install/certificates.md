<h1>Certificates</h1>

SSL certificates should be used to secure web access and connections to virtual desktops with viewers. IsardVDI will generate a default self signed generic certificate when installing from the first time. Also, if no certificate present it will generate a new self signed to make use of it by default. That's why browsers will ask for certificate acceptance on first access to IsardVDI web.

[TOC]

# Manage certificates

Certificates are stored in path **/opt/isard/certs/default** where it can be replaced by new ones. The certificates need to be as follows:

- **server-cert.pem**: It is the full chain of certificate with root cert included.
- **server-key.pem**: It is the server host key.

The ca-cert.pem will be readed from existing server-cert.pem so be aware that the server-cert.pem must contain in first position the server certificate and after that one the chain of validation entity certificates (chain)

## Commercial certificate

Always bring down IsardVDI before proceding to replace certificate:

```
docker-compose down
```

- **server-cert.pem**: You could rename de fullchain given by your cert provider  to be server-cert.pem or you can concatenate server certificate with chain: **`cat myserver.pem ca-chain.pem > server-cert.pem`**
- **server-key.pem**:  Usually will have that key in a file already. Just rename it.

Put those certificates with correct name in **/opt/isard/certs/default** (replace everythig that it is already in that folder) and start IsardVDI again:


```
docker-compose up -d
```

Now you may connect to IsardVDI server using the qualified CN as provided with your certificate.

NOTE: Wilcard certificates have been also validated with this procedure to be working as expected. See example below:

### Example wildcard SSL Certificate

For example you have a wildcard commercial certificate from a company (let's say you bought *.isardvdi.com). You will get this files from your certificate provider:

- **wildcard_isardvdi_com.crt.pem**: Your wilcard certificate.
- **GandiStandardSSLCA2.pem**: This is the intermediate certificate from certificate authority (in this example is gandi.net the provider). You can always get this certificate by copying or exporting from your browser certificate settings or download it from their webpage (i.e. ```wget -q -O - https://www.gandi.net/static/CAs/GandiStandardSSLCA2.pem```)
- **wildcard_isardvdi_com.key.pem**: Your certificate private key.

We will need to transform this files into two needed by IsardVDI:

- **server-cert.pem**: ```cat wildcard_isardvdi_com.crt.pem GandiStandardSSLCA2.pem > /opt/isard/certs/default/server-cert.pem```
- **server-key.pem**: ```mv wildcard_isardvdi_com.key.pem /opt/isard/certs/default/server-key.pem```

## Letsencrypt certificate

Always bring down IsardVDI before proceding to replace certificate:

```
docker-compose down
```

- **server-cert.pem**: It is the fullchain.pem
- **server-key.pem**:  It is the privkey.pem

Put those certificates with correct name in **/opt/isard/certs/default** (replace everything that it is already in that folder) and start IsardVDI again:

```
docker-compose up -d
```

Now you may connect to IsardVDI server using the qualified CN as provided with your certificate.

NOTE: Multihost certificates have been also validated with this procedure to be working as expected.

# Reset certificates

If you replaced certificates and nothing worked it is recommended to start the proccess again by resetting certificates. If you manipulate certificates in the folder it could confuss IsardVDI certificate processing code.

You can always get your IsardVDI working again with self signed certificates by removing /opt/isard/certs/default folder. IsardVDI will generate and configure a new self signed certificate again. Procedure will be:

```bash
docker-compose down
rm -rf /opt/isard/certs/default
docker-compose up
```

You may have done a backup of your previously working self signed certificates and you could now also copy those ones in default certs folder instead of generating new ones.

# Troubleshoot certificates

Please refer to the  [admin faq about certificates](../admin/faq.md#certificates) section.
