
# Testing

```shell
curl -i -H  "fleet-lock-protocol: true" -d @body.json http://IP:PORT/v1/steady-state
```
lets you simulate a steady-state request.
in the same way you can check the pre reboot loocking


---
```shell
curl -i -H  "fleet-lock-protocol: true" -d @body.json http://IP:PORT/v1/pre-reboot
```
----------
```shell
curl -i http://IP:PORT/v1/health
```
lets you check if the Health Endpoint is Reachable. 

----------
```shell
ceph orch ls
```
shows you if the keepalived containers are running

----------

```shell
netstat -tulpn | grep PORT
``` 
to check if the cpeh manager is bindin to that port


----------
```shell
ceph cephlock config-dump
ceph cephlock status
```
to check current config and wich machine is holding a lock

## Manual Lock Releasing

Right now its not possible to release a lock via the CLI.
If you need to do that, edit the body.json file in this folder to match the node that aquired the lock
```shell
ceph cephlock status
```
will tell you which group and uuid is holding the lock

```shell
curl -i -H  "fleet-lock-protocol: true" -d @body.json http://IP:PORT/v1/steady-state
```
will release the lock