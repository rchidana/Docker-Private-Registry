# Docker-Private-Registry

Creating your own Private Docker Registry (Ubuntu 18.04 Linux) with self signed TLS Certificate

Pre-Req : Ubuntu VM with Docker-CE Edition installed on it and required ports open to the outside world (or as required)

Add your Ubuntu VM IP address in subjectAltName in the openssl.cnf before generating certficates

```
sudo vi /etc/ssl/openssl.cnf

```
Add the following with your VM specific IP address in the v3 

```

subjectAltName=IP:IP_ADDRESS_OF_YOUR_VM

```

