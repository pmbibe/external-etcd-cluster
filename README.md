# external-etcd-cluster
Bước 1: \
Đầu tiền cài đặt cụm etcd bằng etcdadm: \
Trên mỗi node sẽ cài đặt etcadm theo hướng dẫn sau: \
https://github.com/kubernetes-sigs/etcdadm \
Bước 2: Cài đặt và cấu hình HA Proxy tương tự như file haproxy.cfg ở trên \
Bước 3: Test thử cài đặt cấu hình cụm etcd đã đúng hay chưa\
docker run --rm -it \
--net host \
-v /etc/etcd/:/etc/etcd/ k8s.gcr.io/etcd:3.4.3-0 etcdctl \
--cert /etc/etcd/pki/peer.crt \
--key /etc/etcd/pki/peer.key \
--cacert /etc/etcd/pki/ca.crt \
--endpoints https://${HOST0}:2379 endpoint health --cluster \
Bước 4: Copy các file sau sang master-host \
export CONTROL_PLANE="root@master-host" \
scp /etc/etcd/pki/ca.crt "${CONTROL_PLANE}":/etc/kubernetes/pki/etcd/ \
scp /etc/etcd/pki/apiserver-etcd-client.crt "${CONTROL_PLANE}":/etc/kubernetes/pki/ \
scp /etc/etcd/pki/apiserver-etcd-client.key "${CONTROL_PLANE}":/etc/kubernetes/pki/ \
Chú ý: Tất cả các master-host đều đã được cài đặt kubelet (Theo file InstallK8S.sh) \
Bước 5: Thiết lập cụm K8s: \
kubeadm init --config kubeadm-config.yaml --upload-certs --v=5 \
Bước 5.5: Thiết lập mạng cho cụm K8s \
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml \
Bước 6: Sang master-host thứ 2: \
scp -R /etc/kubernetes/pki/ 192.168.141.234:/etc/kubernetes/ \
Bước 7: Sử dụng câu lệnh join node : \
kubeadm join 192.168.141.230:6443 --token a8lz23.ol1l00zijwje04wf     --discovery-token-ca-cert-hash sha256:82967c0311fc0bacc5399cd8ab3c6fd205589aa0be58d38736eadc53dcf31fc7 --control-plane \
Bước 8: Join worker node: \
kubeadm join 192.168.141.230:6443 --token la3x9j.go0z60ra3gkbtoqu     --discovery-token-ca-cert-hash sha256:82967c0311fc0bacc5399cd8ab3c6fd205589aa0be58d38736eadc53dcf31fc7 \
echo 1 >> /proc/sys/net/bridge/bridge-nf-call-iptables (Có thể có hoặc không) \
