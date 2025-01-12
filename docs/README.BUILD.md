# Univention Corporate Server - Container Mode

This is a self deploying container for running a [Univention Corporate Server](https://www.univention.com/products/ucs/) ([UCS](https://docs.software-univention.de/manual.html)) with the role of [primary](https://docs.software-univention.de/manual.html#domain-ldap:Primary_Directory_Node), [backup](https://docs.software-univention.de/manual.html#domain-ldap:Backup_Directory_Node), [replica](https://docs.software-univention.de/manual.html#domain-ldap:Replica_Directory_Node) directory node or [managed](https://docs.software-univention.de/manual.html#domain-ldap:Managed_Node) node.

CLI SYNTAX:
```bash
KEY=VALUE; ...; \
  COMMAND \
    --<COMMAND OPTION(S)> \
      <COMMAND ARGUMENT(S)>
...
STDOUT ( succeed )
...
STDOUT ( timeing )
...
```
## Build minbase bootstrap container image from scratch ```( optionally with time )```
Script for docker or podman with debootstrap that imports the first container image to local registry.  But, there are some dependencies for this. You can have a look into the script ``` bootstrap.sh ``` or maybe you will get some instructions to fix them.
```bash
VERSION="5.0-0"; \
  time /bin/bash bootstrap/bootstrap.sh \
    --use-cache \
    --arch amd64 \
    --distribution univention-corporate-server \
    --codename ucs$(echo ${VERSION} | tr --complement --delete '[:digit:]')
...
I: Base system installed successfully.
...
real    1m33,745s
user    1m18,895s
sys     0m15,447s
...
```
If your an non root podman user, an extra step is requerd:
```bash
# sudo sudo tar --create --directory=<...debootstrap...> . | podman import --message "..." - univention-corporate-server-debootstrap:latest
```
For podman users, give this a try in your shell/bash. With or without sudo privileges.
```bash
alias docker="podman"
alias docker="sudo podman"
```
### Inspect the minbase bootstrap container image
```bash
docker image inspect univention-corporate-server-debootstrap:latest
```
## Build a deployment container image ```( optionally with time )```
```bash
VERSION="5.0-0"; IMAGE="univention-corporate-server-debootstrap"; TAG="latest"; \
  time docker build \
    --build-arg DATE=$(date -u +"%Y-%m-%dT%H:%M:%SZ") \
    --build-arg VERSION=${VERSION} \
    --build-arg COMMENT="$(docker image inspect --format '{{.Comment}}' ${IMAGE}:${TAG})" \
    --build-arg IMAGE=${IMAGE} \
    --build-arg TAG=${TAG} \
    --tag univention-corporate-server:${VERSION} \
    --tag univention-corporate-server:${TAG} .
...
Successfully tagged univention-corporate-server:latest
...
real    1m27,116s
user    0m0,193s
sys     0m0,141s
...
```
### Inspect the univention-corporate-server container image
```bash
docker image inspect univention-corporate-server:latest
```
