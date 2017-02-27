# service checker

## examples

### ip and port check

Check ip 138.201.93.253, that the ports 22, 80, 443 are opened and the port 3306 is closed.

```
$ ./checker -i '138.201.93.253' -p+ 22 -p+ 80 -p+ 443 -p- 3306
[2017-02-27 01:09:20] [SUCCESS] The system with ip 138.201.93.253 is running
[2017-02-27 01:09:20] [SUCCESS] The port "22" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "80" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "443" on system with ip "138.201.93.253" is open.
[2017-02-27 01:09:20] [SUCCESS] The port "3306" on system with ip "138.201.93.253" is closed.
```
