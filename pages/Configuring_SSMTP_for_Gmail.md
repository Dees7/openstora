# Configuring SSMTP for Gmail

This allows to send system notifications and errors to our Gmail account. Also it is needed useful to [configure smartmontools](Fixing_smartmontools.md) to send us messages in case of errors.

Edit /etc/ssmtp/ssmtp.conf as follows:
```bash
# This file was generated from a template
# Template name is /etc/oe-admin/templates/system/etc/ssmtp/ssmtp.conf

# The person who gets all mail for userids < 1000
# Make this empty to disable rewriting.
root=<GMAILUSERNAME>@gmail.com

# The place where the mail goes. The actual machine name is required
# no MX records are consulted. Commonly mailhosts are named mail.domain.com
# The example will fit if you are in domain.com and your mailhub is so named.
mailhub=smtp.gmail.com:587

# Example for SMTP port number 2525
# mailhub=mail.your.domain:2525
# Example for SMTP port number 25 (Standard/RFC)
# mailhub=mail.your.domain        
# Example for SSL encrypted connection
# mailhub=mail.your.domain:465

# Where will the mail seem to come from?
rewriteDomain=

# The full hostname
hostname=<YOURHOSTNAME>

# Set this to never rewrite the "From:" line (unless not given) and to
# use that address in the "from line" of the envelope.
FromLineOverride=YES

# Use SSL/TLS to send secure messages to server.
UseSTARTTLS=YES

# Use SSL/TLS certificate to authenticate against smtp host.
#UseTLSCert=YES

# Use this RSA certificate.
#TLSCert=/etc/ssl/certs/ssmtp.pem
AuthUser=<GMAILUSERNAME>
AuthPass=<GMAILPASSWORD>
AuthMethod=LOGIN
```
where \<GMAILUSERNAME\> is your Gmail username (without the @gmail.com), <GMAILPASSWORD> is your Gmail account password and \<YOURHOSTNAME\> is supposed to be the full name of the machine sending the mail although in practice it can be set to anything else.

Also you should edit /etc/ssmtp/ssmtp.revaliases to read
```bash
# sSMTP aliases
# 
# Format:       local_account:outgoing_address:mailhub
#
# Example: root:your_login@your.domain:mailhub.your.domain[:port]
# where [:port] is an optional port number that defaults to 25.

root:<GMAILUSERNAME>@gmail.com:smtp.gmail.com:587
```
It is also useful to add links to sendmail in /usr/sbin since some system programs (as syslogd) expect to find sendmain in /usr/sbin
```
ln -s /usr/bin/ssmtp /usr/sbin/sendmail
```
--J.M. 18:02, 7 March 2011 (UTC) 