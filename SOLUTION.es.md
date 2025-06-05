# Problemas frecuentes y cómo solucionarlos

### 1. El script no encuentra el certificado o la clave
**Mensaje en `report.json`:**
```json
"Certificate File": "❌ No certificate file found. Please check your Apache SSL configuration.",
"Key File": "❌ No private key file found. Please check your Apache SSL configuration."
```

**Causa común:**
- El alumno generó correctamente los archivos `.crt` y `.key`, pero **no los incluyó en ningún archivo `.conf` habilitado** de Apache.

**Solución:**
- Verificar que las líneas `SSLCertificateFile` y `SSLCertificateKeyFile` estén dentro de un sitio activo (por ejemplo `default-ssl.conf`) y que ese sitio esté habilitado con `a2ensite` o mediante enlace manual (ver abajo).


### Habilitar un archivo manualmente sin `a2ensite`

Si por alguna razón `a2ensite` no funciona, puedes habilitar un archivo `.conf` de Apache manualmente:

1. Verifica que el archivo exista

```bash
ls /etc/apache2/sites-available/my-ssl-site.conf
```

2. Crea el enlace simbólico manualmente
```bash
sudo ln -s /etc/apache2/sites-available/my-ssl-site.conf /etc/apache2/sites-enabled/my-ssl-site.conf
```

3. Recarga Apache
```bash
sudo apache2ctl configtest
sudo systemctl reload apache2
```

  **Resultado:** Ahora Apache cargará ese sitio HTTPS correctamente.

---

### 2. El certificado es inválido o expirado

**Mensaje en `report.json`:**
```json
"Certificate Validity": "❌ The certificate has expired. Please generate a new one."
```

**Causa común:**
- El certificado fue generado hace mucho tiempo o con `-days` bajo.

**Solución:**
- Volver a firmar el CSR con `openssl x509` usando `-days 365`.

---

### 3. El certificado es válido pero no coincide con el dominio
**Mensaje en navegador:**
- “Este certificado no es válido para este dominio”

**Causa común:**
- El alumno usó `Common Name: localhost`, pero accede a `https://myserver.com`.

**Solución:**
- Al generar el CSR, debe colocar **el mismo nombre que se usará en el navegador** (ej: `myserver.com`) como Common Name (CN).

---

### 4. Apache no está sirviendo por HTTPS
**Mensaje en `report.json`:**
```json
"SSL Module": "❌ The SSL module is NOT enabled..."
```

**Causa común:**
- El módulo SSL no fue habilitado con `a2enmod ssl`.

**Solución:**
```bash
sudo a2enmod ssl
sudo systemctl reload apache2
```
---

## ✅ Ejemplo de `report.json` correcto

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

## Ejemplo de `report.json` incorrecto

```json
{
  "Apache Service": "✅ Apache is running.",
  "SSL Module": "❌ The SSL module is NOT enabled. Please enable it with: sudo a2enmod ssl...",
  "Certificate File": "❌ No certificate file found. Please check your Apache SSL configuration.",
  "Key File": "❌ No private key file found. Please check your Apache SSL configuration.",
  "Certificate Validity": "❌ Cannot validate the certificate. File not found or invalid format."
}
```

Este informe indica que:
- Apache está funcionando.
- Pero no tiene habilitado el módulo SSL.
- Y no hay archivos `.crt` y `.key` correctamente configurados en sitios activos.
