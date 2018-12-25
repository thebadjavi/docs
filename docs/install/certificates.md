# Managing certificates

SSL certificates should be used to secure web access and connections to virtual desktops with viewers. IsardVDI will generate a default self signed generic certificate when installing from the first time. Also, if no certificate present it will generate a new self signed to make use of it by default. That's why browsers will ask for certificate acceptance on first access to IsardVDI web.

Certificates are stored in path **/opt/isard/certs/default** where it can be replaced by new ones. The certificates need to be as follows:

- **server-cert.pem**: It is the full chain of certificate with root cert included.
- **server-key.pem**: It is the server host key.

The ca-cert.pem will be generated from existing server-cert.pem

## Commercial certificate

Always bring down IsardVDI before proceding to replace certificate:

```
docker-compose down
```

- **server-cert.pem**: You could rename de fullchain given by your cert provider  to be server-cert.pem or you can concatenate server certificate with chain: **`cat myserver.pem ca-chain.pem > server-cert.pem`**
- **server-key.pem**:  Usually will will have that key in a file already. Just rename it.

Put those certificates with correct name in **/opt/isard/certs/default** (replace everythig that it is already in that folder) and start IsardVDI again:

```
docker-compose up -d
```

Now you may connect to IsardVDI server using the qualified CN as provided with your certificate.

NOTE: Multihost certificates have been also validated with this procedure to be working as expected.

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

# Verify updated certificates

## Web browser

The new certificate will be used to access your IsardVDI webserver now. Verify that it is using it in your browser (usually there will be a locker icon before url input).

- In case it is not using new certificate check nginx isard container logs while bringing it up with docker-compose up (withouth daemonizing it with -d). There you will see information about the cert found in folder. 
- Check the certificates now in default folder **/opt/isard/certs/default**. Code should have generated a ca-cert.pem (server certificate extracted from fullchain). You may remove all certificates, put new ones again and start it with docker-compose up.

## Viewers

Certificate info will be shown at menu Hypervisors -> Default pool, showing that it is in **Secure** mode and the **domain info** taken from the updated cert. 

You may connect to a running desktop with spice client and see [[Encrypted]] in window bar. Also connect through vnc or spice websockets and check that it is using https URI with the provided certificate. These are the viewer connections that can be encrypted using certs:

- Spice client (remote-viewer)
- Spice websockets (browser)
- VNC websockets (browser)

Note that VNC client can't be encrypted and should use a tunnel as described in viewers section.

- In case it is not using certificates to access viewers after you replaced certificates and IsardVDI webserver is using it correctly, there must be a server-cert.pem error. IsardVDI extracts a ca-cert.pem from the given server-cert.pem. Please check that extracted ca-cert.pem key is the correct one with:
  - `openssl x509 -in /opt/isard/certs/default/ca-cert.pem -text`
- Verify that you have a full chain with your server certificate first and then your root CA chain of certificates inside server-cert (**`cat myserver.pem ca-chain.pem > server-cert.pem`**)

# Reset certificates

If you replaced certificates and nothing worked check the previous 'Verify updated certs' indications.

You can always get your IsardVDI working again with self signed certificates by removing /opt/isard/certs/default folder. IsardVDI will generate and configure a new self signed certificate again. Procedure will be:

```bash
docker-compose down
rm -rf /opt/isard/certs/default
docker-compose up
```

You could have done a backup of your previous self signed certificates working and you could also copy those ones in default certs folder instead of generating new ones.

 