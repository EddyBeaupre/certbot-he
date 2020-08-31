# Certbot (Let's Encrypt) creation and renewal script for Hurricane Electric DNS service using DJS-01 validation

With this script, domains that are hosted at the Hurricane Electric DNS service are verified automatically using the DNS-01 validation, (as opposed to e.g. webroot validation). It adds a special TXT DNS record for the domain and then removes it when the verification is finished.
 
## Usage:
1. Copy `cerbot-he.conf.example` to `/etc/cerbot-he.conf` and edit it to suit your needs:

```
[DEFAULT]
; Email address is used by Let's Encrypt for important account notifications
Email = <your email>
; RSA Key Size of the cerficate
KeySize = 4096
; If certbot is not in the system's path, specify it's location
#certbot = /usr/bin/certbot
; If certbot-he is not in the system's path, specify it's location
#certbotHe = /usr/local/bin/certbot-he
; Run a command after updating your certificate to do wathever cleanup you need.
#postHook = /bin/systemctl reload apache2

; You can have as many domain section as you want. The first domain will
; also be the certificate name.
[domain.com]
; Key for the DDNS enabled TXT record
Key = DDNS_HE_TXT_KEY
; Request a certificate for this domain.
Domain = True
; Request a wildcard certificate for this domain.
Wildcard = True

; certbot/let's encrypt won't let you create a certificate for a domain that
; is already included in the base domain (in this case, subdomain.domain.com
; is already covered by *.domain.com, but you still can a wildcard for
; *.subdomain.domain.com
[subdomain.domain.com]
; Key for the DDNS enabled TXT record
Key = DDNS_HE_TXT_KEY
; Do not request a certificate for this subdomain
Domain = False
; But request a wildcard certificate for it.
Wildcard = True
```

2. Copy `certbot-he` somewhere in your path (`/usr/bin` or `usr/local/bin`) or make sure `certbotHe` point to it.

3. Create your certificate with `certbot-he certonly`. Add any options you need:

```
Usage: certbot-he <COMMANDS> [OPTIONS]

certbot-he can obtain HTTPS/TLS/SSL certificates. It will use
certbot-he-hook for obtaining certificate. COMMANDS and OPTIONS are:

  certonly              Obtain or renew a certificate, but do not install it
  renew                 Renew all previously obtained certificates that are near expiry

  -t --test-cert        Obtain a test certificate from a staging server
  -d, --dry-run         Test "renew" or "certonly" without saving any certificates to disk
  -e, --expand          Expand and replace an existing certificate
  -a, --agree-tos       Agree to the ACME server's Subscriber Agreement

  -h, --help            Show this help screen
  -v, --version         Show version information

Configuration file is : /etc/certbot-he.conf

Help and support: <https://github.com/EddyBeaupre/certbot-he>
```

4. Add a cron job to update your certificate (`/etc/crowntab` or any other way you like).

```
43 6    * * *   root    /usr/local/bin/certbot-he renew
```

## Bugs

Feel free to submit bugs on Github <https://github.com/EddyBeaupre/certbot-he>.

## License

MIT

## Credits

This is a complete rewrite of Ondrej Simek's `certbot-he-hook` on python, with many aditions to make it a complete certificate management solution that fits all my personal needs. The original script work perfectly and i've used it for many years before i decided to rewrite all my certificate managements scripts into one python script and got the weird idea to rewrite Ondrej's sh script in pyton and incorporate it in my own script.

While i didn't used any of Ondrej's code, credit is well deserved for having the idea to parse the HTML and figure out how dns.he.net handle update. If they could just add a proper API to their service...

Got check the original script at https://github.com/angel333/certbot-he-hook

With the introduction of the dynamic TXT record on dns.he.net, this script no longer use Ondrej's HTML parsing technique. But since the first idea of the script came from him, he still deserve all the credits.
