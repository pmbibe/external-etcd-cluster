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
