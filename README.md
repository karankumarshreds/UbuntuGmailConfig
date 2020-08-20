## Ubuntu Postfix Gmail Configuration

#### Configure Postfix to Use Gmail SMTP on Ubuntu 18.04
- Make sure postfix is install in your server, if not then use the following command: 
```
apt install mailutils
```
- Select *internet site* during the first installation prompt: 
<img src="https://github.com/karankumarshreds/UbuntuGmailConfig/blob/images/first-prompt.PNG" width="400" alt="prompt"/>

- Set the mail name. Mail name specifies the domain part that is used in a mail ID, e.g example.com for an email ID:
<img src="https://github.com/karankumarshreds/UbuntuGmailConfig/blob/images/second-prompt.PNG" width="400" alt="prompt" />

- you can always reconfigure Postfix by running the command below:
```
dpkg-reconfigure postfix
```
- Configure gmail's SMTP server as a mail relay server. Edit the configuration file **/etc/postfix/main.cf** 
```
vim /etc/postfix/main.cf
```
- Find the line, relayhost = and setting its value to Gmail SMTPS such that it looks like:
```
relayhost = [smtp.gmail.com]:587
```
- Add following in the end of the file to :
  - enable Cyrus-SASL support for authentication
  - configure Postfix to use the file with the SASL credentials
  - set the SASL security options to disable options that allows anonymous authentication 
```
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```
