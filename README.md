# service checker

## Usage

### show help

```
$ ./checker --help

Usage: ./checker [options...] <ip>
 -p+,
 -pp,   --port-positive           Checks that this given port is opened on the given machine.

 -p-,   --port-negative           Checks that this given port is closed on the given machine.
 -pn,   -

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

 -ssl,  --check-ssl-certificate   Checks the ssl certificate. Needs a -dn option before.

 -h,    --help                    Shows this help.
 
```

## examples

### 1) simple ip and port check

Check ip 138.201.93.253, that the ports 22, 80, 443 are opened and the port 3306 is closed.

```
$ ./checker -p+ 22 -p+ 80 -p+ 443 -p- 3306 138.201.93.253
[2017-03-12 16:52:42] [PASSED‧] The system with ip 138.201.93.253 is running
[2017-03-12 16:52:42] [PASSED‧] The port "22" on system with ip "138.201.93.253" is open.
[2017-03-12 16:52:42] [PASSED‧] The port "80" on system with ip "138.201.93.253" is open.
[2017-03-12 16:52:42] [PASSED‧] The port "443" on system with ip "138.201.93.253" is open.
[2017-03-12 16:52:42] [PASSED‧] The port "3306" on system with ip "138.201.93.253" is closed.
[2017-03-12 16:52:42] [PASSED‧] All checks passed.
```

### 2) ip, port, domain, status code and ssl check

Check ip 83.169.16.166; Ports 10022, 80 and 443 must be opened; Ports 3306 and 111 must be closed; inter.apo-ident.de and www.inter.apo-ident.de must be assigned to the ip; Unsecure connections must be redirected to the secure one (https://inter.apo-ident.de); Secure connections must serve a 200 status code; The certificates must be valid

```
$ ./checker 83.169.16.166 -p+ 10022 -p+ 80 -p+ 443 -p- 3306 -p- 111 \
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

## if environments.conf is set

You can find an example inside the file environments.conf.dist

```
$ ./checker 
[2017-03-15 01:18:52] [HEADER‧] CHECK CONFIG HIPERSCAN
[2017-03-15 01:18:53] [PASSED‧] The system with ip 83.169.16.166 is running
[2017-03-15 01:18:53] [PASSED‧] The port "10022" on system with ip "83.169.16.166" is open.
[2017-03-15 01:18:53] [PASSED‧] The port "443" on system with ip "83.169.16.166" is open.
[2017-03-15 01:18:53] [PASSED‧] The port "3306" on system with ip "83.169.16.166" is closed.
[2017-03-15 01:18:53] [PASSED‧] The port "111" on system with ip "83.169.16.166" is closed.
[2017-03-15 01:18:53] [PASSED‧] The given domain "inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-15 01:18:53] [PASSED‧] The given domain "www.inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-15 01:18:53] [PASSED‧] The url "http://inter.apo-ident.de" returns the expected status code "301".
[2017-03-15 01:18:53] [PASSED‧] The url "http://inter.apo-ident.de" returns the given compare string "https://inter.apo-ident.de".
[2017-03-15 01:18:53] [PASSED‧] The url "http://www.inter.apo-ident.de" returns the expected status code "301" (one of the expected ports: 404, 301).
[2017-03-15 01:18:53] [PASSED‧] The url "http://www.inter.apo-ident.de" returns the given compare string "https://inter.apo-ident.de".
[2017-03-15 01:18:53] [PASSED‧] The url "https://inter.apo-ident.de" returns the expected status code "200".
[2017-03-15 01:18:53] [PASSED‧] The certificate from the domain "inter.apo-ident.de" was successfully verified.
[2017-03-15 01:18:53] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=AlphaSSL CA - SHA256 - G2" was successfully verified.
[2017-03-15 01:18:53] [PASSED‧] The certificate from the domain "inter.apo-ident.de" is valid until "2018-03-07 14:53".
[2017-03-15 01:18:53] [PASSED‧] The domain "inter.apo-ident.de" validate the domain name in certificate file.
[2017-03-15 01:18:54] [PASSED‧] The ocsp is activated on domain "inter.apo-ident.de" ("http://ocsp2.globalsign.com/gsalphasha2g2").
[2017-03-15 01:18:54] [PASSED‧] All checks passed.
[2017-03-15 01:18:54] [HEADER‧] CHECK CONFIG ECEFACEBOOK
[2017-03-15 01:18:54] [PASSED‧] The system with ip 91.250.103.239 is running
[2017-03-15 01:18:54] [PASSED‧] The port "5122" on system with ip "91.250.103.239" is open.
[2017-03-15 01:18:54] [PASSED‧] The port "80" on system with ip "91.250.103.239" is open.
[2017-03-15 01:18:54] [PASSED‧] The port "443" on system with ip "91.250.103.239" is open.
[2017-03-15 01:18:54] [PASSED‧] The port "3306" on system with ip "91.250.103.239" is closed.
[2017-03-15 01:18:54] [PASSED‧] The port "111" on system with ip "91.250.103.239" is closed.
[2017-03-15 01:18:54] [PASSED‧] The given domain "www.ece-fb.de" is assigned to ip "91.250.103.239".
[2017-03-15 01:18:54] [PASSED‧] The given domain "ece-fb.de" is assigned to ip "91.250.103.239".
[2017-03-15 01:18:55] [PASSED‧] The url "https://admin:Udababumu480@www.ece-fb.de/admin/dashboard" returns the expected status code "200".
[2017-03-15 01:18:55] [PASSED‧] The url "http://www.ece-fb.de" returns the expected status code "301".
[2017-03-15 01:18:55] [PASSED‧] The url "http://www.ece-fb.de" returns the given compare string "https://www.ece-fb.de".
[2017-03-15 01:18:55] [PASSED‧] The url "http://ece-fb.de" returns the expected status code "301".
[2017-03-15 01:18:55] [PASSED‧] The url "http://ece-fb.de" returns the given compare string "https://www.ece-fb.de".
[2017-03-15 01:18:56] [PASSED‧] The url "https://www.ece-fb.de/admin/dashboard" returns the expected status code "401".
[2017-03-15 01:18:56] [PASSED‧] The url "https://www.ece-fb.de" returns the expected status code "301".
[2017-03-15 01:18:56] [PASSED‧] The url "https://www.ece-fb.de" returns the given compare string "https://www.ece-fb.de/admin/dashboard".
[2017-03-15 01:18:57] [PASSED‧] The certificate from the domain "www.ece-fb.de" was successfully verified.
[2017-03-15 01:18:57] [PASSED‧] The chainfile from the issuer "/C=US/O=Let's Encrypt/CN=Let's Encrypt Authority X3" was successfully verified.
[2017-03-15 01:18:57] [PASSED‧] The certificate from the domain "www.ece-fb.de" is valid until "2017-05-19 02:05".
[2017-03-15 01:18:57] [PASSED‧] The domain "www.ece-fb.de" validate the domain name in certificate file.
[2017-03-15 01:18:57] [PASSED‧] The ocsp is activated on domain "www.ece-fb.de" ("http://ocsp.int-x3.letsencrypt.org/").
[2017-03-15 01:18:57] [PASSED‧] All checks passed.
[2017-03-15 01:18:57] [HEADER‧] CHECK CONFIG BMEL
[2017-03-15 01:18:57] [PASSED‧] The system with ip 46.163.114.68 is running
[2017-03-15 01:18:57] [PASSED‧] The port "22" on system with ip "46.163.114.68" is open.
[2017-03-15 01:18:57] [PASSED‧] The port "80" on system with ip "46.163.114.68" is open.
[2017-03-15 01:18:57] [PASSED‧] The port "443" on system with ip "46.163.114.68" is open.
[2017-03-15 01:18:57] [PASSED‧] The given domain "www.haustier-berater.de" is assigned to ip "46.163.114.68".
[2017-03-15 01:18:57] [PASSED‧] The given domain "haustier-berater.de" is assigned to ip "46.163.114.68".
[2017-03-15 01:18:57] [PASSED‧] The given domain "www.500landinitiativen.de" is assigned to ip "46.163.114.68".
[2017-03-15 01:18:57] [PASSED‧] The given domain "500landinitiativen.de" is assigned to ip "46.163.114.68".
[2017-03-15 01:18:58] [PASSED‧] The url "https://500landinitiativen.de" returns the expected status code "301".
[2017-03-15 01:18:58] [PASSED‧] The url "http://haustier-berater.de" returns the expected status code "301".
[2017-03-15 01:18:58] [PASSED‧] The url "https://www.500landinitiativen.de" returns the expected status code "200".
[2017-03-15 01:18:58] [PASSED‧] The url "https://haustier-berater.de" returns the expected status code "301".
[2017-03-15 01:18:58] [PASSED‧] The url "http://www.haustier-berater.de" returns the expected status code "301".
[2017-03-15 01:18:58] [PASSED‧] The url "http://500landinitiativen.de" returns the expected status code "301".
[2017-03-15 01:18:58] [PASSED‧] The url "https://www.haustier-berater.de" returns the expected status code "200".
[2017-03-15 01:18:58] [PASSED‧] The url "http://www.500landinitiativen.de" returns the expected status code "301".
[2017-03-15 01:18:59] [PASSED‧] The certificate from the domain "www.500landinitiativen.de" was successfully verified.
[2017-03-15 01:18:59] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=AlphaSSL CA - SHA256 - G2" was successfully verified.
[2017-03-15 01:18:59] [PASSED‧] The certificate from the domain "www.500landinitiativen.de" is valid until "2017-11-15 15:56".
[2017-03-15 01:18:59] [PASSED‧] The domain "www.500landinitiativen.de" validate the domain name in certificate file.
[2017-03-15 01:18:59] [PASSED‧] The ocsp is activated on domain "www.500landinitiativen.de" ("http://ocsp2.globalsign.com/gsalphasha2g2").
[2017-03-15 01:18:59] [PASSED‧] The certificate from the domain "www.haustier-berater.de" was successfully verified.
[2017-03-15 01:18:59] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=GlobalSign Domain Validation CA - SHA256 - G2" was successfully verified.
[2017-03-15 01:18:59] [PASSED‧] The certificate from the domain "www.haustier-berater.de" is valid until "2017-12-08 16:55".
[2017-03-15 01:18:59] [PASSED‧] The domain "www.haustier-berater.de" validate the domain name in certificate file.
[2017-03-15 01:19:00] [PASSED‧] The ocsp is activated on domain "www.haustier-berater.de" ("http://ocsp2.globalsign.com/gsdomainvalsha2g2").
[2017-03-15 01:19:00] [PASSED‧] All checks passed.
[2017-03-15 01:19:00] [HEADER‧] RESULT
[2017-03-15 01:19:00] [PASSED‧] All checks passed
```
