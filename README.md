```bash
docker run --interactive --rm quay.io/coreos/butane:release --pretty --strict < proxmox.bu > proxmox.ign
```
```bash
coreos-installer install /dev/sda -I https://raw.githubusercontent.com/orginux/lab-cluster-k8s/main/proxmox.ign
```

If some issues with startup OS need add:
```bash
--append-karg console=tty0
```

```bash
ssh -i fedora-coreos core@fedora-coreos-0.local
```

```bash
python3 -m venv ven
source venv/bin/activate
pip install -r requirements.txt
```

```bash
ansible-playbook -i inventory/fcos-lab/inventory.ini --become --key-file ~/.ssh/proxmox/fedora-coreos cluster.yml
```

[Deploy CH](https://github.com/Altinity/clickhouse-operator/blob/master/docs/operator_installation_details.md)
Check deployment:
```bash
kubectl get deployments --namespace kube-system | grep 'clickhouse-operator'
```
