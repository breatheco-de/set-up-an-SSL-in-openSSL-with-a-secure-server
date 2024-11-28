<!-- hide -->
# Setting Up a Secure Server with SSL/TLS Using OpenSSL

> By [@rosinni](https://github.com/rosinni) and [other contributors](https://github.com/breatheco-de/set-up-an-SSL-in-openSSL-with-a-secure-server/graphs/contributors) at [4Geeks Academy](https://4geeksacademy.co/)

[![build by developers](https://img.shields.io/badge/build_by-Developers-blue)](https://4geeks.com)
[![build by developers](https://img.shields.io/twitter/follow/4geeksacademy?style=social&logo=twitter)](https://twitter.com/4geeksacademy)

*Estas instrucciones están [disponibles en español](https://github.com/breatheco-de/set-up-an-SSL-in-openSSL-with-a-secure-server/blob/main/README.md)*

### Before Starting...

> We need you! These exercises are created and maintained in collaboration with people like you. If you find any errors or typos, please contribute and/or report them.

<!-- endhide -->

<onlyfor saas="false" withBanner="false">

## 🌱 How to start this project?

This exercise aims to teach students how to set up a secure server using OpenSSL to provide secure communications via SSL/TLS.

### Requirements

* A Debian virtual machine installed in VirtualBox. (we will use the previously configured machine in previous classes).

</onlyfor>

## 📝 Instructions

* Open this URL and fork the repository https://github.com/breatheco-de/set-up-an-SSL-in-openSSL-with-a-secure-server

 ![fork button](https://github.com/4GeeksAcademy/4GeeksAcademy/blob/master/site/src/static/fork_button.png?raw=true)

A new repository will be created in your account.

* Clone the newly created repository into your localhost computer.
* Once you have cloned successfully, follow the steps below carefully, one by one.

### Step 1: Generate a private key and a Certificate Signing Request (CSR):
- [ ] Open a terminal and execute the following command to generate a 2048-bit RSA private key:
    ```sh
    openssl genrsa -out /etc/ssl/private/myserver.key 2048
    ```
> 💡Make sure to protect this private key properly.

<!-- ### Step 2: Generate a Certificate Signing Request (CSR): -->

- [ ] Use the following command to generate a CSR that will contain public information included in the certificate:

    ```sh
    openssl req -new -key /etc/ssl/private/myserver.key -out /etc/ssl/certs/myserver.csr
    ```
**During the process, you will be prompted to enter information about your organization.** 
   (Here's an example of how you can complete it):
  * Country Name (2 letter code): ES
  * State or Province Name (full name): Madrid
  * Locality Name (eg, city): Madrid
  * Organization Name (eg, company): MiEmpresa
  * Organizational Unit Name (eg, section): IT
  * Common Name (eg, fully qualified host name): mi-dominio.com
  * Email Address: admin@mi-dominio.com


### Step 2: Sign the CSR to Obtain a Self-Signed Certificate:
- [ ] For the purpose of this practice, sign the CSR with your own private key to obtain a self-signed certificate valid for 365 days. Use the following command:

    ```sh
    openssl x509 -req -days 365 -in /etc/ssl/certs/myserver.csr -signkey /etc/ssl/private/myserver.key -out /etc/ssl/certs/myserver.crt
    ```

### Step 3: Configure Apache to Use the SSL Certificate:
- [ ] Verify the Apache SSL configuration file:

    ```sh
    sudo nano /etc/apache2/sites-available/default-ssl.conf
    ```

- [ ] Ensure the file contains the following:

      ```sh
      <IfModule mod_ssl.c>
          <VirtualHost _default_:443>
              ServerAdmin admin@mi-dominio.com
              ServerName mi-dominio.com

              DocumentRoot /var/www/html

              SSLEngine on
              SSLCertificateFile /etc/ssl/certs/myserver.crt
              SSLCertificateKeyFile /etc/ssl/private/myserver.key

              <FilesMatch "\.(cgi|shtml|phtml|php)$">
                  SSLOptions +StdEnvVars
              </FilesMatch>
              <Directory /usr/lib/cgi-bin>
                  SSLOptions +StdEnvVars
              </Directory>

              BrowserMatch "MSIE [2-6]" \
                  nokeepalive ssl-unclean-shutdown \
                  downgrade-1.0 force-response-1.0

              BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

          </VirtualHost>
      </IfModule>
      ```
### Step 4: Enable SSL Site and SSL Module:
- [ ] Use the following commands:

    ```sh
    sudo a2enmod ssl
    sudo a2ensite default-ssl
    ```
### Step 5: Update the Hosts File:
- [ ] Verify the /etc/hosts file on your local machine (from where you access the virtual machine) to ensure mi-dominio.com resolves to 127.0.0.1:

    ```sh
    sudo nano /etc/hosts
    ```
  > 💡 Make sure to add the line:
    * 127.0.0.1 mi-dominio.com

- [ ]  Restart the virtual machine to apply all changes


### Step 6: Test the Connection:
- [ ] Open a web browser and enter the URL https://mi-dominio.com. You should see a security warning due to the self-signed certificate. Accept the risk and continue to view the default Apache page served via HTTPS.

![mi-dominio.com](assets/https.png)


> 💡 NOTE: For the purpose of this educational exercise, while using localhost with HTTPS (https://localhost/) suffices to demonstrate basic SSL/TLS configuration using OpenSSL, including the setup of a custom domain like mi-dominio.com provides a more comprehensive and practical learning experience. This additional step allows understanding of how DNS resolution works in a real environment. When generating the SSL/TLS certificate, it is crucial that the Common Name matches the domain used to access the server, thus avoiding security warnings and errors in web browsers. This reinforces understanding of essential concepts and enhances practical skills necessary for handling SSL/TLS configurations in a professional environment.

## 🚛 How to deliver this project?

We have developed a script to help you measure your success during this project.

- [ ] In the ./assets folder, you will find the script check_ssl.sh which you should copy and paste onto the desktop of your Debian virtual machine.

- [ ] Once you have pasted the script check_ssl.sh onto your Debian machine, open the terminal and navigate to the directory where the script is located, in our case ./Desktop, and make the script executable (if it is not already). This can be done using the chmod command:

```sh
chmod +x check_ssl.sh
```

- [ ] Run the script by specifying its name. You may also need to provide any necessary arguments. Assuming no additional arguments are needed for this example, you should run:

```sh
./check-rules.sh
```

- [ ] Upload your results. Running the script will create a report.json file, which you should copy and paste into the root of this project.
