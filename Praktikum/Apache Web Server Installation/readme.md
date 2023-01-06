- Installing and configuring the web server package
- Install the httpd package and its dependencies.

```
sudo dnf install httpd
```
- Enable and start the httpd service.

This command enables and starts the httpd service for immediate use and also starts the service automatically after a system reboot:

```
sudo systemctl enable --now httpd.service
```
To check the status of the service, type:
```
sudo systemctl status httpd
```
- (Optional) If you are using a custom firewall profile or an Oracle Cloud Infrastructure instance, open the firewall port for the Apache web service (80).

These commands enable the firewall port for the Apache web service and reload the default firewall service:
```
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```
- Test the deployment.

With your web browser, go to the domain name or IP address of your system, for example, http://myserver.example.com. The Apache web server opens the default test page.

## Configuring Apache
To change the root path for your web server, do not edit the /etc/httpd/conf/httpd.conf file directly. Instead, as a preferred method, create a site-specific configuration file in the /etc/httpd/conf.d directory.

In the following example, the file /etc/httpd/conf.d/example.com.conf is created to contain configurations for virtual hosts.

- Create virtual hosts by adding the following information in /etc/httpd/conf.d/example.com.conf:

```
Listen *:80

<VirtualHost *:80>

ServerName example.com
ServerAlias www.example.com

DocumentRoot /var/www/example.com/html/

ErrorLog /var/log/httpd/example.com_error.log
CustomLog /var/log/httpd/example.com_access.log combined

</VirtualHost>
```
ServerName and ServerAlias can be hostnames, domain names, or IP addresses that can be used to access the service.

- Create the document root folder.

The root folder hosts the web pages that Apache would provide to browsers.

```
sudo mkdir -p /var/www/example.com/html
```
```
sudo echo "example.com" > /var/www/example.com/html/index.html
```
```
sudo chown -R apache:apache /var/www/example.com/html
```
On systems where SELinux is enabled in enforcing mode and pages are not served from within the /var/www directory, you must apply the correct security context to the DocumentRoot directory. For example, to serve web pages from the /mnt/example.com directory, type:

```
sudo semanage fcontext -a -t httpd_sys_content_t "/mnt/example.com(/.*)?"
sudo restorecon -Rv /mnt/example.com/
```
- Apply your configuration.

```
sudo systemctl restart httpd
```
## Securing the web service
As a best practice, secure all communications between a web browser and your Apache server by using HTTPS. For a secure setup, a TLS certificate is required.

- Configure your TLS/SSL certificates.
Note:
Oracle strongly recommends that you use a TLS certificate that has been signed by an external Certificate Authority (CA). See Oracle Linux: Managing Certificates and Public Key Infrastructure for more information.

- Install the HTTPS add-on for Apache.
```
sudo dnf install mod_ssl
sudo systemctl restart httpd
```
- Update the Apache configuration. For example, in /etc/httpd/conf.d/example.com.conf, add the following configuration:
```
Listen *:443

<VirtualHost *:443>

ServerName example.com
ServerAlias www.example.com

SSLEngine on SSLCertificateFile /etc/pki/tls/private/certificate.crt
SSLCertificateKeyFile /etc/pki/tls/private/private.key

DocumentRoot /var/www/example.com/html/

ErrorLog /var/log/httpd/example.com_error.log
CustomLog /var/log/httpd/example.com_access.log combined

</VirtualHost>
```
- Redirect HTTP requests to HTTPS.

Replace your existing HTTP VirtualHost configuration with a redirect, as follows:
```
<VirtualHost *:80>

ServerName example.com
ServerAlias www.example.com

Redirect “/” “https://example.com/”

</VirtualHost>
```
- Restart the Apache service to load the new configuration.
```
sudo systemctl restart httpd
```
- (Optional) Configure the firewall.

These commands enable the firewall port 443 for the Apache HTTPS service and reloads the default firewall service:
```
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```
