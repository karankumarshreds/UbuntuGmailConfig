## Ubuntu Postfix Gmail Configuration

#### Configure Postfix to Use Gmail SMTP on Ubuntu 18.04
Make sure postfix is install in your server, if not then use the following command: 
> apt install mailutils

Select *internet site* during the first installation prompt: 

<img src="https://github.com/karankumarshreds/UbuntuGmailConfig/blob/images/first-prompt.PNG" width="400" alt="prompt"/>

Set the mail name. Mail name specifies the domain part that is used in a mail ID, e.g example.com for an email ID:

<img src="https://github.com/karankumarshreds/UbuntuGmailConfig/blob/images/second-prompt.PNG" width="400" alt="prompt" />

You can always reconfigure Postfix by running the command below:
> dpkg-reconfigure postfix

Configure gmail's SMTP server as a mail relay server. Edit the configuration file **/etc/postfix/main.cf** 
> vim /etc/postfix/main.cf

Change inet_protocols = all to inet_protocols = ipv4

Find the line, relayhost = and setting its value to Gmail SMTPS such that it looks like:
```
relayhost = [smtp.gmail.com]:587
```
Add following in the end of the file to :
 - enable Cyrus-SASL support for authentication
 - configure Postfix to use the file with the SASL credentials
 - set the SASL security options to disable options that allows anonymous authentication 
```
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```
#### Enable STRTTLS Encryption
Enforce STARTTLS encryption for outgoing SMTP with Postfix by adding the following line to the same file:
```
smtp_tls_security_level = encrypt
```
In the same file, define the path to CA certificates. 
The public root certificates are usually found under /etc/ssl/certs/ca-certificates.crt:
```
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```
##### Final file should look like : 
```
...
relayhost = [smtp.gmail.com]:587
...
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```
#### Add Credentials to sasl_passwd
Since Postfix is acting a as mail client, it has to know when to provide a username and password. Therefore, create the sasl_passwd file define above, /etc/postfix/sasl_passwd and set the credentials of the mail relay server as shown below:
> $ vim /etc/postfix/sasl_passwd
```
[smtp.gmail.com]:587 userid@gmail.com:password
```
#### Secure sasl_passwd
The credentials are set in plaintext. Hence to make it abit secured, change ownership and permission to root and read-write only respectively.
> chown root:root /etc/postfix/sasl_passwd

> chmod 600 /etc/postfix/sasl_passwd

#### Create sasl_passwd DB file
Postfix requires that the sasl_passwd file to be a database such that it can be read faster. Use postmap command to convert the file into a database, sasl_passwd.db.
> postmap /etc/postfix/sasl_passwd

#### Restart Postfix

> sudo systemctl restart postfix

### Send a Test Mail
To verify that all is well, send the test mail as shown below;
```
echo "Test Postfix Gmail Relay" | mail -s "Postfix Gmail Relay" userid@gmail.com
```

### NOTE
Login to your gmail account and go to security settings and *give access to less sucure apps*.

Secondly, go to this link and allow : https://accounts.google.com/b/0/DisplayUnlockCaptcha

Thirdly, make sure the domain you selected while setting up in the initial steps is **different** from the domain **to** which you are sending emails.
