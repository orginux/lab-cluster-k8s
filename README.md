## OS
```bash
docker run --interactive --rm quay.io/coreos/butane:release --pretty --strict < fcos-proxmox.yml > fcos-proxmox.ign
```
```bash
coreos-installer install /dev/sda -I https://raw.githubusercontent.com/orginux/lab-cluster-k8s/main/fcos-proxmox.ign
```

If some issues with startup OS need add:
```bash
--append-karg console=tty0
```

```bash
ssh -i fedora-coreos core@fedora-coreos-0.local
```

## Kubernetes

Prepare:
```bash
git clone git@github.com:kubernetes-sigs/kubespray.git
git clobe git@github.com:orginux/kubespray-inventory.git
ln -s kubespray-inventory/fcos-lab/ kubespray/inventory/
```
Review and change parameters under `kubernetes-inventory/`

Install requirements:
```bash
python3 -m venv ven
source venv/bin/activate
pip install -r requirements.txt
```

Deploy k8s:
```bash
ansible-playbook -i inventory/fcos-lab/inventory.ini cluster.yml
```

## ClickHouse
[Deploy CH operator](https://github.com/Altinity/clickhouse-operator/blob/master/docs/operator_installation_details.md)
Check deployment:
```bash
kubectl get deployments --namespace kube-system | grep 'clickhouse-operator'
```

Deploy Zookeeper:
```bash
k create ns zoons
k apply -n zoons -f https://raw.githubusercontent.com/Altinity/clickhouse-operator/master/deploy/zookeeper/quick-start-volume-emptyDir/zookeeper-1-node.yaml
```

For deploy used [kustomize](https://kustomize.io)
Create CH cluster:
```bash
kustomize build chi/ | k apply -f -
```

Try connect to CH:
Terminal 1:
```bash
k port-forward service/clickhouse-simple-chi 9000:9000
```
Terminal 2:
```bash
docker run --network host --rm -it yandex/clickhouse-client:21.8.10.19 --host 127.0.0.1 --user clickhouse_operator --ask-password
```
Check cluster:
```sql
SELECT cluster, shard_num, replica_num, host_name FROM system.clusters;
```

## Create test table
[ClickHouse doc](https://clickhouse.com/docs/en/sql-reference/statements/insert-into/)
```sql
CREATE TABLE insert_select_testtable
(
    `a` Int8,
    `b` String,
    `c` Int8
)
ENGINE = MergeTree()
ORDER BY a
```
```sql
INSERT INTO insert_select_testtable (*) VALUES (1, 'a', 1) ;
```
```sql
SELECT * FROM insert_select_testtable;
```

## Create distributet table
https://cloud.yandex.com/en-ru/docs/managed-clickhouse/tutorials/sharding
