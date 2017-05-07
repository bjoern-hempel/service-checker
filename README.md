# service checker

The service checker is a tool to do some server checks. It can send emails if any check is failed. It can check the server accessibility, open and closed ports, assigned websites, returned http status codes, ssl certificates and so on. With the environment files you can easily use the checks with some cronjob tasks.

## A.) Installation

### A.1) the simplest way

If you trust me, you can use this simple way to install the service-checker. Please have a look at https://www.ixno.de/service-checker/install before, if you want to know what this script makes.

```
user$ wget -O ~/sc-install -q https://www.ixno.de/service-checker/install && sudo bash ~/sc-install; rm -f ~/sc-install
```

Now you can use the service-checker like this:

```
user$ service-checker --help

Usage: service-checker [options...] <ip>
...
```

### A.2) global installation with git (root credentials needed)

```
user$ mkdir ~/service-checker
user$ cd ~/service-checker
user$ git clone git://github.com/bjoern-hempel/service-checker.git .
user$ sudo ./install -g
user$ cd ..
user$ rm -r service-checker
user$ service-checker --help

Usage: service-checker [options...] <ip>
...
```

### A.3) local installation with git (no root credentials needed)

For example if you want to install this checker into your own home directory or you don't have root credentials.

```
user$ mkdir ~/service-checker
user$ cd ~/service-checker
user$ git clone git://github.com/bjoern-hempel/service-checker.git .
user$ ./install
user$ bin/checker --help

Usage: bin/checker [options...] <ip>
...
```

### A.4) show help

```
user$ service-checker --help

Usage: service-checker [options...] <ip>
 -p+,
 -pp,   --port-positive           Checks that this given port is opened on the given machine.

 -p-,
 -pn,   --port-negative           Checks that this given port is closed on the given machine.

 -sc,   --status-code             Checks that a given url returns an expected status code (http:80). Needs a -dn option before.

        option (-dn domain.tld)                           url check                                     expected return
        ---------------------------------------------------------------------------------------------------------------------------------------------------------
        -sc 301                                           http://domain.tld                             301
        -sc >                                             http://domain.tld                             301
        -sc 301=https://domain.tld                        http://domain.tld                             301 with target https://domain.tld
        -sc >https                                        http://(domain.tld)                           301 with target https://$1
        -sc >/redirect/home.html                          http://(domain.tld)                           301 with target http://$1/redirect/home.html

 -ssc,  --secure-status-code      Checks that a given url returns an expected status code (https:443). Needs a -dn option before.

        option (-dn domain.tld)                           url check                                     expected return
        ---------------------------------------------------------------------------------------------------------------------------------------------------------
        -ssc 401                                          https://domain.tld                            401 (password protection)
        -ssc user:pass@200                                https://user:pass@domain.tld                  200
        -cre user:pass                                    >> set credentials for the following checks
        -ssc 200                                          https://user:pass@domain.tld                  200
        -ssc user:wrongpass@401                           https://user:wrongpass@domain.tld             401
        -cre-                                             >> remove credentials
        -ssc 401                                          https://domain.tld                            401 (password protection)
        -ssc user:pass@404:/server-status                 https://user:pass@domain.tld/server-status    404
        -ssc 404:/nonexisting.html                        https://domain.tld/nonexisting.html           404
        -ssc 404,401:/typo3                               https://domain.tld/typo3                      404 or 401
        -ssc 404,401,301=https://typo3.domain.tld:/typo3  https://domain.tld/typo3                      404 or 401 or 301 with target https://typo3.domain.tld

 -dn,   --set-domainname          Sets the current domain name. With this set domain name other commands can be combined.
 -cre,  --set-credenial           Sets the current webserver credentials. With this set credentials other commands can be combined.
 -cre-, --remove-credenial        Removes the current set webserver credentials.
 -ssl,  --check-ssl-certificate   Checks the ssl certificate. Needs a -dn option before.

 -em,   --email                   Email addresses (comma separated) from recipients of the success and failed mails.
 -fe,   --from-email              From email address (used for success and failed mails).
 -er,   --email-relay             Change the email relay strategy (direct, docker:[name]).
 -to,   --time-out                Time out for email delivery (avoid mass email delivery)
 -json, --json-file               Adds the json log output.
 -log,  --log-file                Adds the log output.
 -id,   --identifier              An optional identifier which will be used for some information channels like email, etc. (if not given it uses the ip address)

 -dp,   --disable-ping            Disable the ping check.

 -sdo,  --suppress-direct-output  1 - Suppress all directly printed outputs to screen (excluding log outputs, etc.); 0 - Don't suppress

 -h,    --help                    Shows this help.
```

## 1.) examples

### 1.1) check that given system is awake (ping check)

Check ip 46.163.114.68.

```
user$ service-checker 46.163.114.68
[2017-05-07 01:12:13] [PASSED]  [system.awake]                               The system with ip 46.163.114.68 is running
[2017-05-07 01:12:13] [PASSED]  [overall]                                    All checks passed.
```

### 1.2) disable the ping check

Disable the ping check (-dp).

```
$ service-checker 46.163.114.68 -dp
[2017-05-07 01:38:55] [PASSED]  [overall]                                    All checks passed.
```

### 1.3) check that given ports are reachable

Check that the ports 22, 80 and 443 are open (-p+).

```
user$ service-checker 46.163.114.68 -p+ 22 -p+ 80 -p+ 443
[2017-05-07 01:19:04] [PASSED]  [system.awake]                               The system with ip 46.163.114.68 is running
[2017-05-07 01:19:04] [PASSED]  [ports.positive.22]                          The port "22" on system with ip "46.163.114.68" is open.
[2017-05-07 01:19:04] [PASSED]  [ports.positive.80]                          The port "80" on system with ip "46.163.114.68" is open.
[2017-05-07 01:19:04] [PASSED]  [ports.positive.443]                         The port "443" on system with ip "46.163.114.68" is open.
[2017-05-07 01:19:04] [PASSED]  [overall]                                    All checks passed.
```

### 1.4) check that given ports are not reachable

Port 111 and 25 should be closed (-p-).

```
$ service-checker 46.163.114.68 -p- 111 -p- 25
[2017-05-07 01:19:04] [PASSED]  [system.awake]                               The system with ip 46.163.114.68 is running
[2017-05-07 01:45:41] [PASSED]  [ports.negative.111]                         The port "111" on system with ip "46.163.114.68" is closed.
[2017-05-07 01:45:43] [PASSED]  [ports.negative.25]                          The port "25" on system with ip "46.163.114.68" is closed.
[2017-05-07 01:45:43] [PASSED]  [overall]                                    All checks passed.
```

### 1.5) check that the given domains is assigned to the ip

The given domains should be assigned to the ip 46.163.114.68.

```
user$ service-checker 46.163.114.68 -dn www.bienenfuettern.de -dn bienenfuettern.de
[2017-05-07 02:08:15] [PASSED]  [system.awake]                               The system with ip 46.163.114.68 is running
[2017-05-07 02:08:15] [PASSED]  [domains.bienenfuettern.de]                  The given domain "bienenfuettern.de" is assigned to ip "46.163.114.68".
[2017-05-07 02:08:15] [PASSED]  [domains.www.bienenfuettern.de]              The given domain "www.bienenfuettern.de" is assigned to ip "46.163.114.68".
[2017-05-07 02:08:15] [PASSED]  [overall]                                    All checks passed.
```

### 1.6) check status codes


### 1.7) check ssl certificates

### 1.8) ip, port, domain, status code and ssl check

Check ip 83.169.16.166; Ports 10022, 80 and 443 must be opened; Ports 3306 and 111 must be closed; inter.apo-ident.de and www.inter.apo-ident.de must be assigned to the ip; Unsecure connections must be redirected to the secure one (https://inter.apo-ident.de); Secure connections must serve a 200 status code; The certificates must be valid

```
$ service-checker 83.169.16.166 -p+ 10022 -p+ 80 -p+ 443 -p- 3306 -p- 111 \
-dn inter.apo-ident.de -sc 301=https://inter.apo-ident.de -ssc 200 -ssl \
-dn www.inter.apo-ident.de -sc 404,301=https://inter.apo-ident.de
[2017-03-12 16:43:38] [PASSED‧] The system with ip 83.169.16.166 is running
[2017-03-12 16:43:38] [PASSED‧] The port "10022" on system with ip "83.169.16.166" is open.
[2017-03-12 16:43:38] [PASSED‧] The port "80" on system with ip "83.169.16.166" is open.
[2017-03-12 16:43:38] [PASSED‧] The port "443" on system with ip "83.169.16.166" is open.
[2017-03-12 16:43:38] [PASSED‧] The port "3306" on system with ip "83.169.16.166" is closed.
[2017-03-12 16:43:38] [PASSED‧] The port "111" on system with ip "83.169.16.166" is closed.
[2017-03-12 16:43:38] [PASSED‧] The given domain "inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-12 16:43:38] [PASSED‧] The given domain "www.inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-12 16:43:38] [PASSED‧] The url "http://inter.apo-ident.de" returns the expected status code "301".
[2017-03-12 16:43:38] [PASSED‧] The url "http://inter.apo-ident.de" returns the given compare string "https://inter.apo-ident.de".
[2017-03-12 16:43:38] [PASSED‧] The url "http://www.inter.apo-ident.de" returns the expected status code "301" (one of the expected ports: 404, 301).
[2017-03-12 16:43:38] [PASSED‧] The url "http://www.inter.apo-ident.de" returns the given compare string "https://inter.apo-ident.de".
[2017-03-12 16:43:38] [PASSED‧] The url "https://inter.apo-ident.de" returns the expected status code "200".
[2017-03-12 16:43:38] [PASSED‧] The certificate from the domain "inter.apo-ident.de" was successfully verified.
[2017-03-12 16:43:38] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=AlphaSSL CA - SHA256 - G2" was successfully verified.
[2017-03-12 16:43:39] [PASSED‧] The certificate from the domain "inter.apo-ident.de" is valid until "2018-03-07 14:53".
[2017-03-12 16:43:39] [PASSED‧] The domain "inter.apo-ident.de" validate the domain name in certificate file.
[2017-03-12 16:43:39] [PASSED‧] The ocsp is activated on domain "inter.apo-ident.de" ("http://ocsp2.globalsign.com/gsalphasha2g2").
[2017-03-12 16:43:39] [PASSED‧] All checks passed.
```

## 2.) use environments

The checker script uses the environments.conf inside the root folder (mostly inside the folder /opt/service-checker) or directly from /etc/service-checker/environments.conf. You can find some examples within the file environments.conf.dist. With the environment files you can save some server check settings without entering every time the check configs. Instead you can use an environment name or the a selection list to start the test.

### 2.1) example content for environments.conf

```
# METRO PPD (live):
# -----------------
# check 194.156.54.59
#   - rsmso.ppd.metro.de
#   - api, etc.
metroppd {
    name=metroppd
    ip=194.156.54.59
    parameter="
        -p+ 80 -p+ 443 -p- 22
        -dn rsmso.ppd.metro.de
            -ssc 401
            -cre rsm:rsm2016
            -sc 301=https://rsmso.ppd.metro.de
            -ssc 200=ausgeliefert:/v1.0/info
            -ssl
        -dn home.ppd.metro.de
            -cre-
            -sc 301=https://home.ppd.metro.de
            -ssc 200
            -ssl
        --json-file=/home/bjoern/logs/metroppd/json/metroppd.json
        --log-file=/home/bjoern/logs/metroppd/log/metroppd.log
        --email=bjoern.hempel@ressourcenmangel.de,nadine.sander@ressourcenmangel.de
        --from-email=monitoring@dd3.rsm-development.de
        --time-out=1800
    "
}
```

### 2.2) usage (with choice)

```
$ service-checker 

The following system test environments are available:

No Name                 IP
------------------------------------------------
1) ALL                  ALL
2) metroppd             194.156.54.59

Choose the environment number you would like to test: 2

[2017-04-25 01:52:39] [HEADER] CHECK CONFIG METROPPD
[2017-04-25 01:52:39] [PASSED] [system.awake]                               The system with ip 194.156.54.59 is running
[2017-04-25 01:52:39] [PASSED] [ports.positive.80]                          The port "80" on system with ip "194.156.54.59" is open.
[2017-04-25 01:52:39] [PASSED] [ports.positive.443]                         The port "443" on system with ip "194.156.54.59" is open.
[2017-04-25 01:52:41] [PASSED] [ports.negative.22]                          The port "22" on system with ip "194.156.54.59" is closed.
[2017-04-25 01:52:41] [PASSED] [domains.rsmso.ppd.metro.de]                 The given domain "rsmso.ppd.metro.de" is assigned to ip "194.156.54.59".
[2017-04-25 01:52:41] [PASSED] [domains.home.ppd.metro.de]                  The given domain "home.ppd.metro.de" is assigned to ip "194.156.54.59".
[2017-04-25 01:52:42] [PASSED] [statusCodes.case1]                          ┏━  The url "https://rsm:rsm2016@rsmso.ppd.metro.de/v1.0/info" returns the expected status code "200".
                                                                            ┃   The url "https://rsm:rsm2016@rsmso.ppd.metro.de/v1.0/info" returns the given compare string:
                                                                            ┗━  "ausgeliefert".
[2017-04-25 01:52:42] [PASSED] [statusCodes.case2]                          ┏━  The url "http://home.ppd.metro.de" returns the expected status code "301".
                                                                            ┃   The url "http://home.ppd.metro.de" returns the given compare string:
                                                                            ┗━  "https://home.ppd.metro.de".
[2017-04-25 01:52:42] [PASSED] [statusCodes.case3]                          ┏━  The url "http://rsm:rsm2016@rsmso.ppd.metro.de" returns the expected status code "301".
                                                                            ┃   The url "http://rsm:rsm2016@rsmso.ppd.metro.de" returns the given compare string:
                                                                            ┗━  "https://rsmso.ppd.metro.de".
[2017-04-25 01:52:42] [PASSED] [statusCodes.case4]                          The url "https://rsmso.ppd.metro.de" returns the expected status code "401".
[2017-04-25 01:52:42] [PASSED] [statusCodes.case5]                          The url "https://home.ppd.metro.de" returns the expected status code "200".
[2017-04-25 01:52:43] [PASSED] [certificates.rsmso.ppd.metro.de.verified]   The certificate from the domain "rsmso.ppd.metro.de" was successfully verified.
[2017-04-25 01:52:43] [PASSED] [certificates.rsmso.ppd.metro.de.chain]      ┏━  The chainfile from the issuer
                                                                            ┃   "/C=GB/ST=Greater Manchester/L=Salford/O=COMODO CA Limited/CN=COMODO RSA Organization Validation Secure Server CA"
                                                                            ┗━  was successfully verified.
[2017-04-25 01:52:43] [PASSED] [certificates.rsmso.ppd.metro.de.valid]      The certificate from the domain "rsmso.ppd.metro.de" is valid until "2017-11-26 00:59".
[2017-04-25 01:52:43] [PASSED] [certificates.rsmso.ppd.metro.de.domainName] The domain "rsmso.ppd.metro.de" validate the domain name in certificate file.
[2017-04-25 01:52:43] [PASSED] [certificates.rsmso.ppd.metro.de.ocsp]       The ocsp is activated on domain "rsmso.ppd.metro.de" ("http://ocsp.comodoca.com").
[2017-04-25 01:52:43] [PASSED] [certificates.home.ppd.metro.de.verified]    The certificate from the domain "home.ppd.metro.de" was successfully verified.
[2017-04-25 01:52:44] [PASSED] [certificates.home.ppd.metro.de.chain]       ┏━  The chainfile from the issuer
                                                                            ┃   "/C=GB/ST=Greater Manchester/L=Salford/O=COMODO CA Limited/CN=COMODO RSA Organization Validation Secure Server CA"
                                                                            ┗━  was successfully verified.
[2017-04-25 01:52:44] [PASSED] [certificates.home.ppd.metro.de.valid]       The certificate from the domain "home.ppd.metro.de" is valid until "2017-11-26 00:59".
[2017-04-25 01:52:44] [PASSED] [certificates.home.ppd.metro.de.domainName]  The domain "home.ppd.metro.de" validate the domain name in certificate file.
[2017-04-25 01:52:44] [PASSED] [certificates.home.ppd.metro.de.ocsp]        The ocsp is activated on domain "home.ppd.metro.de" ("http://ocsp.comodoca.com").
[2017-04-25 01:52:44] [PASSED] [overall]                                    All checks passed.
[2017-04-25 01:52:44] [INFO]   Ignore sending success mail to bjoern.hempel@ressourcenmangel.de (no error mail before) (identifier: metroppd).
```

### 2.3) usage (directly use the choice)

```
user$ service-checker metroppd
[2017-04-25 01:53:29] [HEADER] CHECK CONFIG METROPPD
[2017-04-25 01:53:29] [PASSED] [system.awake]                               The system with ip 194.156.54.59 is running
[2017-04-25 01:53:29] [PASSED] [ports.positive.80]                          The port "80" on system with ip "194.156.54.59" is open.
[2017-04-25 01:53:29] [PASSED] [ports.positive.443]                         The port "443" on system with ip "194.156.54.59" is open.
[2017-04-25 01:53:31] [PASSED] [ports.negative.22]                          The port "22" on system with ip "194.156.54.59" is closed.
[2017-04-25 01:53:31] [PASSED] [domains.rsmso.ppd.metro.de]                 The given domain "rsmso.ppd.metro.de" is assigned to ip "194.156.54.59".
[2017-04-25 01:53:31] [PASSED] [domains.home.ppd.metro.de]                  The given domain "home.ppd.metro.de" is assigned to ip "194.156.54.59".
[2017-04-25 01:53:32] [PASSED] [statusCodes.case1]                          ┏━  The url "https://rsm:rsm2016@rsmso.ppd.metro.de/v1.0/info" returns the expected status code "200".
                                                                            ┃   The url "https://rsm:rsm2016@rsmso.ppd.metro.de/v1.0/info" returns the given compare string:
                                                                            ┗━  "ausgeliefert".
[2017-04-25 01:53:32] [PASSED] [statusCodes.case2]                          ┏━  The url "http://home.ppd.metro.de" returns the expected status code "301".
                                                                            ┃   The url "http://home.ppd.metro.de" returns the given compare string:
                                                                            ┗━  "https://home.ppd.metro.de".
[2017-04-25 01:53:32] [PASSED] [statusCodes.case3]                          ┏━  The url "http://rsm:rsm2016@rsmso.ppd.metro.de" returns the expected status code "301".
                                                                            ┃   The url "http://rsm:rsm2016@rsmso.ppd.metro.de" returns the given compare string:
                                                                            ┗━  "https://rsmso.ppd.metro.de".
[2017-04-25 01:53:32] [PASSED] [statusCodes.case4]                          The url "https://rsmso.ppd.metro.de" returns the expected status code "401".
[2017-04-25 01:53:32] [PASSED] [statusCodes.case5]                          The url "https://home.ppd.metro.de" returns the expected status code "200".
[2017-04-25 01:53:33] [PASSED] [certificates.rsmso.ppd.metro.de.verified]   The certificate from the domain "rsmso.ppd.metro.de" was successfully verified.
[2017-04-25 01:53:33] [PASSED] [certificates.rsmso.ppd.metro.de.chain]      ┏━  The chainfile from the issuer
                                                                            ┃   "/C=GB/ST=Greater Manchester/L=Salford/O=COMODO CA Limited/CN=COMODO RSA Organization Validation Secure Server CA"
                                                                            ┗━  was successfully verified.
[2017-04-25 01:53:33] [PASSED] [certificates.rsmso.ppd.metro.de.valid]      The certificate from the domain "rsmso.ppd.metro.de" is valid until "2017-11-26 00:59".
[2017-04-25 01:53:33] [PASSED] [certificates.rsmso.ppd.metro.de.domainName] The domain "rsmso.ppd.metro.de" validate the domain name in certificate file.
[2017-04-25 01:53:33] [PASSED] [certificates.rsmso.ppd.metro.de.ocsp]       The ocsp is activated on domain "rsmso.ppd.metro.de" ("http://ocsp.comodoca.com").
[2017-04-25 01:53:33] [PASSED] [certificates.home.ppd.metro.de.verified]    The certificate from the domain "home.ppd.metro.de" was successfully verified.
[2017-04-25 01:53:34] [PASSED] [certificates.home.ppd.metro.de.chain]       ┏━  The chainfile from the issuer
                                                                            ┃   "/C=GB/ST=Greater Manchester/L=Salford/O=COMODO CA Limited/CN=COMODO RSA Organization Validation Secure Server CA"
                                                                            ┗━  was successfully verified.
[2017-04-25 01:53:34] [PASSED] [certificates.home.ppd.metro.de.valid]       The certificate from the domain "home.ppd.metro.de" is valid until "2017-11-26 00:59".
[2017-04-25 01:53:34] [PASSED] [certificates.home.ppd.metro.de.domainName]  The domain "home.ppd.metro.de" validate the domain name in certificate file.
[2017-04-25 01:53:34] [PASSED] [certificates.home.ppd.metro.de.ocsp]        The ocsp is activated on domain "home.ppd.metro.de" ("http://ocsp.comodoca.com").
[2017-04-25 01:53:34] [PASSED] [overall]                                    All checks passed.
[2017-04-25 01:53:34] [INFO]   Ignore sending success mail to bjoern.hempel@ressourcenmangel.de (no error mail before) (identifier: metroppd).
```
