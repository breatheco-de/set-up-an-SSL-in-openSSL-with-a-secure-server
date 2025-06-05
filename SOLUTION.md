# Common Problems and How to Solve Them

### 1. The script cannot find the certificate or the key
**Message in `report.json`:**
```json
"Certificate File": "❌ No certificate file found. Please check your Apache SSL configuration.",
"Key File": "❌ No private key file found. Please check your Apache SSL configuration."
```

**Common Cause:**
- The student correctly generated the `.crt` and `.key` files, but **did not include them in any enabled `.conf` file** of Apache.

**Solution:**
- Check that the lines `SSLCertificateFile` and `SSLCertificateKeyFile` are within an active site (for example, `default-ssl.conf`) and that this site is enabled with `a2ensite` or through manual linking (see below).


### Manually Enable a File Without `a2ensite`

If for some reason `a2ensite` does not work, you can manually enable an Apache `.conf` file:

1. Verify that the file exists

```bash
ls /etc/apache2/sites-available/my-ssl-site.conf
```

2. Create the symbolic link manually
```bash
sudo ln -s /etc/apache2/sites-available/my-ssl-site.conf /etc/apache2/sites-enabled/my-ssl-site.conf
```

3. Reload Apache
```bash
sudo apache2ctl configtest
sudo systemctl reload apache2
```

    **Result:** Now Apache will load that HTTPS site correctly.

---

### 2. The certificate is invalid or expired

**Message in `report.json`:**
```json
"Certificate Validity": "❌ The certificate has expired. Please generate a new one."
```

**Common Cause:**
- The certificate was generated a long time ago or with a low `-days` value.

**Solution:**
- Re-sign the CSR with `openssl x509` using `-days 365`.

---

### 3. The certificate is valid but does not match the domain
**Message in browser:**
- “This certificate is not valid for this domain”

**Common Cause:**
- The student used `Common Name: localhost`, but accesses `https://myserver.com`.

**Solution:**
- When generating the CSR, they must place **the same name that will be used in the browser** (e.g., `myserver.com`) as the Common Name (CN).

---

### 4. Apache is not serving over HTTPS
**Message in `report.json`:**
```json
"SSL Module": "❌ The SSL module is NOT enabled..."
```

**Common Cause:**
- The SSL module was not enabled with `a2enmod ssl`.

**Solution:**
```bash
sudo a2enmod ssl
sudo systemctl reload apache2
```
---

## ✅ Example of a correct `report.json`

```json
{
    "Apache Service": "✅ Apache is running.",
    "SSL Module": "✅ The SSL module is enabled.",
    "Certificate File": "✅ Found certificate at /etc/ssl/certs/myserver.crt",
    "Key File": "✅ Found private key at /etc/ssl/private/myserver.key",
    "Certificate Validity": "✅ Valid certificate. Common Name (CN): myserver.com",
    "Days Until Expiry": "✅ The certificate will expire in 365 days."
}
```

---

## Example of an incorrect `report.json`

```json
{
    "Apache Service": "✅ Apache is running.",
    "SSL Module": "❌ The SSL module is NOT enabled. Please enable it with: sudo a2enmod ssl...",
    "Certificate File": "❌ No certificate file found. Please check your Apache SSL configuration.",
    "Key File": "❌ No private key file found. Please check your Apache SSL configuration.",
    "Certificate Validity": "❌ Cannot validate the certificate. File not found or invalid format."
}
```

This report indicates that:
- Apache is running.
- But the SSL module is not enabled.
- And there are no `.crt` and `.key` files correctly configured in active sites.
