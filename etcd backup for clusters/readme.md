# BACKUP Rancher etcd


**Note.. Rancher backs up to Etcd (snapshout in rancher language) to a local dir.**

**This backup Rancher for just one cluster, if you have two or more clusters. You will need to check names on rancher management cluster local dir /var/lib/rancher/rke2/server/db/snapshots/ . Then update script if each cluster or add a loop to this script.**


# prerequisite 
 
**check etcd backup is running. It should be running as default on new rke2 managemnt cluster, backing up all servers.**
 
 
# get one ETCD server
```diff
ETCD_SERVERS=`kubectl get nodes -o wide | grep etcd | awk '{print $1;}' |tail --lines=1` 
 ```
# create script to create pod that has access to local files. i.e. /var/lib/rancher/rke2/server/db/snapshots/etcd*

```diff
cat << EOF > backup-etcd-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: backup-etcd
  labels:
    env: test
spec:
  nodeName: $ETCD_SERVERS
  containers:
  - name: nginx
  - 
    image: nginx
    imagePullPolicy: IfNotPresent
    volumeMounts:
      - name: my-volume
      - 
        mountPath: /var/lib/rancher/rke2/server/db/snapshots
  volumes:
    - name: my-volume
      hostPath:
        path: /var/lib/rancher/rke2/server/db/snapshots
EOF

```
 
# install pod to link to local dir on ETCD node
```diff
kubectl apply -f backup-etcd-pod.yaml
``` 
# find and copy file for pod to local Dir

```diff
BACKUP_FILE=`kubectl exec -i backup-etcd -n default -- ls -ltr /var/lib/rancher/rke2/server/db/snapshots/ | grep etcd | tail --lines=1 | awk '{print $(NF)}'`
```
# A - copy file form pod
```diff
kubectl exec -i backup-etcd -n default -- cat /var/lib/rancher/rke2/server/db/snapshots/$BACKUP_FILE >  $BACKUP_FILE
```
# B - compress if needed (i.e. 25M to 5M)
```diff
tar -czvf xxxx.tar.zip etcd*
 ```
# del pod
```diff
kubectl delete pods backup-etcd
```
#   End   
#
#

#  Restore  **not tested** , same steps above apart for steps A+B 
 
# B - uncompress back file
```diff
tar -xzvf  $BACKUP_FILE
```
# A - copy back to etcd server
```diff
cat $BACKUP_FILE | kubectl exec -i  backup-etcd -n default -- tee /var/lib/rancher/rke2/server/db/snapshots/$BACKUP_FILE >/dev/null
```
# **********  End     **************
