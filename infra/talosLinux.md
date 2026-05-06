# Install Talos Linux for Kubernetes

1. Configurando o talos com talosctl
talosctl gen config homelabs https://192.168.0.150:6443

valida os discos
talosctl -n 192.168.0.150 get disks --insecure

para controlplane e worker no mesmo host:
sed -i 's/# allowSchedulingOnControlPlanes: true/allowSchedulingOnControlPlanes: true/g' controlplane.yaml 

# aplicando as configurações no talos
talosctl apply-config -n 192.168.0.150 --insecure --file controlplane.yaml

# reboot talos server and remove the usb beacuse the boot

# Install kubernetes
talosctl bootstrap --nodes 192.168.0.150 --endpoints 192.168.0.150 --talosconfig=./talosconfig

talosctl config endpoint 192.168.0.150
export TALOSCONFIG=/home/carlos.tomelin/codes/tomelin/talos/talosconfig

# Merge as configurações do talos kubeconfgi e kubeconfig
talosctl kubeconfig -n 192.168.0.150

talosctl health -n 192.168.0.150

cat patch-dns.yaml
machine:
  network:
    nameservers:
      - 8.8.8.8
      - 8.8.4.4
      - 1.1.1.1

 talosctl -n 192.168.0.150 patch machineconfig --patch-file patch-dns.yaml


talosctl -n 192.168.0.150 --endpoints 192.168.0.150 patch machineconfig --patch @patch-dns.yaml

# Apply config in kube-proxy, after cluster running
kube-proxy: There's a caveat here - kube-proxy is a bootstrap manifest, it lives fully in Kubernetes. Talos by default doesn't update them, so you need to run talosctl upgrade-k8s to the same Kubernetes version to get things updated. 
talosctl upgrade-k8s -n 192.168.0.150 -e 192.168.0.150
# patch-kubelet-san.yaml
machine:
  kubelet:
    extraArgs:
      rotate-server-certificates: "true"

# Liste os CSRs pendentes
kubectl get csr

# Aprove
kubectl certificate approve <nome-do-csr>
