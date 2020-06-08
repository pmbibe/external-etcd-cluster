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
a

