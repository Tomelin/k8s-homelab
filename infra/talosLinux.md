# Install Talos Linux for Kubernetes


 cat patch-dns.yaml
machine:
  network:
    nameservers:
      - 8.8.8.8
      - 8.8.4.4
      - 1.1.1.1

 talosctl -n 192.168.0.150 patch machineconfig --patch-file patch-dns.yaml


  talosctl -n 192.168.0.150 --endpoints 192.168.0.150 \
>   patch machineconfig --patch @patch-dns.yaml
# patch-kubelet-san.yaml
machine:
  kubelet:
    extraArgs:
      rotate-server-certificates: "true"

# Liste os CSRs pendentes
kubectl get csr

# Aprove
kubectl certificate approve <nome-do-csr>
