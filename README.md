# Deployment frappe framework menggunakan Kubernetes

## Prequisites

```shell
helm repo add frappe https://helm.erpnext.com
helm pull frappe/erpnext
tar -xzvf  erpnext-4.0.24.tgz
```


## `values.yaml`

Sesuaikan value `storageClass` yang ada pada cluster
```yaml
persistence:
  worker:
    enabled: true
    size: 8Gi
    storageClass: standard
```
```yaml
  logs:
    enabled: true
    size: 8Gi
    storageClass: standard
```

Sesuaikan `siteName` dengan domain

```yaml
createSite:
    enabled: true
    forceCreate: true
    siteName: "localhost"
    adminPassword: "praktis2022"
    installApps:
    - "erpnext"
    dbType: "mariadb"
```
```yaml
  dropSite:
    enabled: false
    forced: true
    siteName: "localhost"
```

```yaml
backup:
    enabled: true
    siteName: "localhost"
    withFiles: true
```

```yaml
  migrate:
    enabled: false
    siteName: "localhost"
```

Sesuaikan auth pada `mariadb`
```yaml
mariadb:
  enabled: true
  auth:
    rootPassword: "praktis2022"
    username: "praktis"
    password: "praktis2022"
    replicationPassword: "praktis2022"
  primary:
    service:
      ports:
        mysql: 3306
```

Additional config untuk external database host

```yaml
dbHost: "<deployname>.<namespace>.svc.cluster.local"
dbPort: 3306
dbRootUser: root
dbRootPassword: praktis2022
```

## Install helm deploy pada Kubernetes

```shell
cd Helm-Chart/erp-next
helm install <release name> -f values.yaml . -n <namespace>
```

Cek semua resources dan pastikan semua running

![image](https://user-images.githubusercontent.com/64014794/171695694-db34d89d-1d7b-4e0a-9555-76d580cf0685.png)

Logs sukses ketika membuat sites baru

![image](https://user-images.githubusercontent.com/64014794/171714390-812fd79d-83b5-42b0-8852-f748530b4732.png)

Logs sukses ketika backup sites

![image](https://user-images.githubusercontent.com/64014794/171718384-b4c86307-9c8b-49ed-8a73-db41bc6adddd.png)


Akses menggunakan `localhost` dengan port-forward untuk login ke desk `frappe`
```shell
kubectl port-forward pod/<nginx-pod> -n <namespace> 8080:8080
```
Tampilan desk frappe

![image](https://user-images.githubusercontent.com/64014794/171696435-3b64fe32-b1fa-44ac-b97f-2a7e47970de4.png)

