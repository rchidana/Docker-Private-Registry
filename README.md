# Docker-Private-Registry

Creating your own Private Docker Registry (Ubuntu 18.04 Linux) with self signed TLS Certificate

Pre-Req : Ubuntu VM with Docker-CE Edition installed on it and required ports open to the outside world (or as required)

Add your Ubuntu VM IP address in subjectAltName in the openssl.cnf before generating certficates

```
sudo vi /etc/ssl/openssl.cnf

```
Add the following with your VM specific IP address under the section [ v3_ca ] 

```
[ v3_ca ]
subjectAltName=IP:IP_ADDRESS_OF_YOUR_VM

```

Create a local folder which will hold the certificates and that can be referenced by the Docker Registry server

```
mkdir -p /certificates

cd certificates

openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout domain.key \
  -x509 -days 365 -out domain.crt
  
#Enter all required fields (it could be anything) but please enter your Server IP address when it prompts for -> Common Name (e.g. server FQDN or YOUR name)

Common Name (e.g. server FQDN or YOUR name) []: IP_ADDRESS_OF_YOUR_VM

# Check if the certificates are created in the current directory (certificates)

ls

```

Launch Docker registry using version 2 and referencing the certificates folder for TLS

```

sudo docker run -d -p 5000:5000 --restart=always --name registry \
  -v /certificates:/certificates \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certificates/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certificates/domain.key \
  registry:2
  
docker ps
docker logs CONTAINER-ID

#Check & proceed further if there are no errors in the registry container log

```

To verify our Docker registry, let us pull a small hello-world docker image from Docker-Hub registry, tag it appropriately and try to push it to our local Registry

```

docker pull hello-world
docker tag hello-world IP_ADDRESS_OF_YOUR_VM:5000/hello-world

docker push IP_ADDRESS_OF_YOUR_VM:5000/hello-world

```

Certificate Error? Docker Server does not trust the self-signed certificate and our certificates need to be manually added to Docker Engine

```
sudo mkdir -p /etc/docker/certs.d/IP_ADDRESS_OF_YOUR_VM:5000

#Please make sure to copy domain.crt as ca.crt (or rename later on) 
sudo cp /certificates/domain.crt /etc/docker/certs.d/IP_ADDRESS_OF_YOUR_VM:5000/ca.crt

sudo ls /etc/docker/certs.d/IP_ADDRESS_OF_YOUR_VM:5000/

#reload docker daemon to use the ca.crt certificate
sudo service docker reload

```

Try pushing to the local registry server now!!
