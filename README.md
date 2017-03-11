# service checker

## examples

### ip and port check

Check ip 138.201.93.253, that the ports 22, 80, 443 are opened and the port 3306 is closed.

```
$ ./checker -p+ 22 -p+ 80 -p+ 443 -p- 3306 138.201.93.253
[2017-02-27 01:09:20] [SUCCESS] The system with ip 138.201.93.253 is running
[2017-02-27 01:09:20] [SUCCESS] The port "22" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "80" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "443" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "3306" on system with ip "138.201.93.253" is closed.
```

Check ip 83.169.16.166; Ports 10022, 80 and 443 must be opened; Ports 3306 and 111 must be closed; inter.apo-ident.de and www.inter.apo-ident.de must be assigned to the ip; Unsecure connections must be redirected to the secure one; Secure connections must serve a 200 status code; The certificates must be valid

```
$ ./checker 83.169.16.166 -p+ 10022 -p+ 80 -p+ 443 -p- 3306 -p- 111 -dn inter.apo-ident.de -sc 301 -ssc 200 -ssl -dn www.inter.apo-ident.de -sc 301 -ssl
[2017-03-11 16:19:53] [PASSED‧] The system with ip 83.169.16.166 is running
[2017-03-11 16:19:53] [PASSED‧] The port "10022" on system with ip "83.169.16.166" is open.
[2017-03-11 16:19:53] [PASSED‧] The port "80" on system with ip "83.169.16.166" is open.
[2017-03-11 16:19:53] [PASSED‧] The port "443" on system with ip "83.169.16.166" is open.
[2017-03-11 16:19:53] [PASSED‧] The port "3306" on system with ip "83.169.16.166" is closed.
[2017-03-11 16:19:53] [PASSED‧] The port "111" on system with ip "83.169.16.166" is closed.
[2017-03-11 16:19:53] [PASSED‧] The given domain "inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-11 16:19:53] [PASSED‧] The given domain "www.inter.apo-ident.de" is assigned to ip "83.169.16.166".
[2017-03-11 16:19:53] [PASSED‧] The url "http://inter.apo-ident.de" returns the expected status code "301".
[2017-03-11 16:19:53] [PASSED‧] The url "http://www.inter.apo-ident.de" returns the expected status code "301".
[2017-03-11 16:19:53] [PASSED‧] The url "https://inter.apo-ident.de" returns the expected status code "200".
[2017-03-11 16:19:53] [PASSED‧] The certificate from the domain "inter.apo-ident.de" was successfully verified.
[2017-03-11 16:19:53] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=AlphaSSL CA - SHA256 - G2" was successfully verified.
[2017-03-11 16:19:53] [PASSED‧] The certificate from the domain "inter.apo-ident.de" is valid until "2018-03-07 14:53".
[2017-03-11 16:19:54] [PASSED‧] The ocsp is activated on domain "inter.apo-ident.de" ("http://ocsp2.globalsign.com/gsalphasha2g2").
[2017-03-11 16:19:54] [PASSED‧] The certificate from the domain "www.inter.apo-ident.de" was successfully verified.
[2017-03-11 16:19:54] [PASSED‧] The chainfile from the issuer "/C=BE/O=GlobalSign nv-sa/CN=AlphaSSL CA - SHA256 - G2" was successfully verified.
[2017-03-11 16:19:54] [PASSED‧] The certificate from the domain "www.inter.apo-ident.de" is valid until "2018-03-07 14:53".
[2017-03-11 16:19:55] [PASSED‧] The ocsp is activated on domain "www.inter.apo-ident.de" ("http://ocsp2.globalsign.com/gsalphasha2g2").
[2017-03-11 16:19:55] [PASSED‧] All checks passed.
```
