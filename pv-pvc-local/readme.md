 
# copy tar file to PVC dir form server and dir in pv-1.yml
 
kubectl create -f storageClass.yaml #**not neded yif you already have**

# update with a node you would like to same to local disks. i.e. 343-k8-cluster-10 

kubectl create -f pv-1.yml

# to test bu ssh into pod, not needed. But heplfull

kubectl create -f pod.yml  #to copy back file to and then remove. not needed

# ##in rancher gui. create a backup and select the pcv you made and localdisk in options.

#  location, check node name to add to yaml file.

kubectl get pod -owide   # check location, just for checking

kubectl describe pod pod-storage-name #just to test if file is there

kubectl exec -it pod-storage-name -- sh # check location, just for checking

cd /test1 # check location, just for checking

ls 


# restore to server of the same name i.e.  1.server.me

 
 
helm repo add rancher-charts https://charts.rancher.io

helm repo update
 
 
helm search repo --versions rancher-charts/rancher-backup
 
CHART_VERSION=2.1.5
 
helm install rancher-backup-crd rancher-charts/rancher-backup-crd -n cattle-resources-system --create-namespace --version $CHART_VERSION
 
helm install rancher-backup rancher-charts/rancher-backup -n cattle-resources-system --values=./values-rancher-backup.yaml  --version $CHART_VERSION
 
kubectl apply -f restore-migration.yaml

kubectl logs -n cattle-resources-system --tail 100 -f -l app.kubernetes.io/instance=rancher-backup

kubectl get restore

NAME              BACKUP-SOURCE   BACKUP-FILE                                                                    AGE   STATUS

restore-default   PV              backuptest1-3de624c9-xxx-48f7-a507-a9e802f109b0-2023-08-01T11-00-33Z.tar.gz   16h   Completed
 
#kubectl delete -f restore-migration.yaml

#kubectl describe Restore
 
 
kubectl create namespace cattle-system
 
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=backup.test1 \
  --set bootstrapPassword=admin \
  --set global.cattle.psp.enabled=false
 
