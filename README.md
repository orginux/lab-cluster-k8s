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
