# check you/cinder storage
 
Server info
https://backup.test1/dashboard/home
admin / test12345678
 
Link tom pod copy  # https://fabianlee.org/2022/09/10/kubernetes-copying-files-into-and-out-of-containers-without-kubectl-cp/
 
 
Prerequisites
1, cinder (or other) storage as default.
#kubectl get storageclasses.storage.k8s.io
NAME                    PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
caas-cinder (default)   cinder.csi.openstack.org   Delete          Immediate           false                  77d
2, unsupported-storage-drivers may need enabled - GlobalSettings/featureFlags/unsupported-storage-drivers
3, kubectl connection to the local management rancher server via $KUBECONFIG
 
Backup Script
helm repo add rancher-chart https://charts.rancher.io
helm repo update
#install backup operator and pvc setup
helm install rancher-backup-crd rancher-chart/rancher-backup-crd -n cattle-resources-system --create-namespace
helm install rancher-backup rancher-chart/rancher-backup -n cattle-resources-system  --values=./values-rancher-backup.yaml
#run backup and copy files to pwd 
kubectl apply -f ./testbackup.yaml
BACKUP_POD=`kubectl get pod -owide -n cattle-resources-system | grep rancher-backup- | cut -d " " -f1`
BACKUP_FILE=`kubectl exec -i $BACKUP_POD -n cattle-resources-system -- ls -ltr /var/lib/backups/ | grep .gz |tail --lines=1 |  awk '{print $(NF)}'`
kubectl exec -i $BACKUP_POD -n cattle-resources-system -- cat /var/lib/backups/$BACKUP_FILE >  $BACKUP_FILE
#etcd backup
files  = /var/lib/rancher/rke2/server/db/snapshots/etcd-snapshot-testback1-pool1-840ccb63-bj6hc-1691496000
 
Restore Script
#extra Prerequisites, have the backup file ready for your variable path setting.
i.e. BACKUP_FILE=testbackup-3de624c9-9b1b-48f7-a507-a9e802f109b0-2023-08-10T09-52-16Z.tar.gz
#Go
helm repo add rancher-chart https://charts.rancher.io
helm repo update
#check cinder is available, else create
kubectl get storageclasses.storage.k8s.io 
helm search repo --versions rancher-charts/rancher-backup
CHART_VERSION=2.1.5
#install backup operator and it will add PV PVC to copy backup fine into.
helm install rancher-backup-crd rancher-charts/rancher-backup-crd -n cattle-resources-system --create-namespace --version $CHART_VERSION
helm install rancher-backup rancher-charts/rancher-backup -n cattle-resources-system --values=./values-rancher-backup.yaml  --version $CHART_VERSION
#find rancher-backup-*  pod 
BACKUP_POD=`kubectl get pod -owide -n cattle-resources-system | grep rancher-backup- | cut -d " " -f1`
#find local backup file
BACKUP_FILE=testbackup-3de624c9-9b1b-48f7-a507-a9e802f109b0-2023-08-10T09-52-16Z.tar.gz
#copy backup file into PVC on rancher-backup-*  pod 
cat $BACKUP_FILE | kubectl exec -i $BACKUP_POD -n $ns -- tee /var/lib/backups/$BACKUP_FILE >/dev/null
#create restore-migration.yaml file, with backup file name within.
cat << EOF > restore-migration.yaml
apiVersion: resources.cattle.io/v1
kind: Restore
metadata:
  name: restore-default
spec:
  backupFilename: $BACKUP_POD 
  storageLocation: null
EOF
#run restore command for rancher backup operator
kubectl apply -f restore-migration.yaml
# Check restore logs if you like !!!
kubectl delete -f restore-migration.yaml
kubectl describe Restore
#Install rancher
kubectl create namespace cattle-system
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=backup.test1 \
  --set bootstrapPassword=admin \
  --set global.cattle.psp.enabled=false
