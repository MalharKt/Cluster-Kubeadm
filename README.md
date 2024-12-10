First Execute the master script check if the nodes in the cluster are visible
by command 
kubectl get nodes
Then the worker script and if there is any error like 

  ERROR

error in joining node with master

ubuntu@ip-172-31-30-22:~/kubeadm-scripts$ kubeadm join 172.31.30.22:6443 --token xpachq.3t0o8e9zror574px \
        --discovery-token-ca-cert-hash sha256:f5909641d840d71b2217093b59c5cc5ac27a49613063c445b0ea87283741c9cb
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR IsPrivilegedUser]: user is not running as root
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher


SOLUTION

ubuntu@ip-172-31-25-18:~$
ubuntu@ip-172-31-25-18:~$ sudo kubeadm reset
W1209 10:54:34.303016    2923 preflight.go:56] [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y

ubuntu@ip-172-31-25-18:~$ sudo rm -rf /etc/kubernetes/
sudo rm -rf /var/lib/etcd/
sudo rm -rf /var/lib/kubelet/*

ubuntu@ip-172-31-25-18:~$ sudo lsof -i :10250
ubuntu@ip-172-31-25-18:~$ sudo systemctl restart containerd
ubuntu@ip-172-31-25-18:~$ sudo kubeadm join 172.31.30.22:6443 --token xpachq.3t0o8e9zror574px \
    --discovery-token-ca-cert-hash sha256:f5909641d840d71b2217093b59c5cc5ac27a49613063c445b0ea87283741c9cb
[preflight] Running pre-flight checks
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.



ERROR

ubuntu@ip-172-31-25-18:~$ kubectl get pods
E1209 10:50:35.767309    2916 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1209 10:50:35.767603    2916 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1209 10:50:35.768690    2916 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1209 10:50:35.768909    2916 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1209 10:50:35.770162    2916 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
The connection to the server localhost:8080 was refused - did you specify the right host or port?

SOLUTION

ubuntu@ip-172-31-25-18:/$ mkdir -p ~/.kube

ubuntu@ip-172-31-25-18:/$ cd ~/.kube
ubuntu@ip-172-31-25-18:~/.kube$ ls
ubuntu@ip-172-31-25-18:~/.kube$ vim config   (copy the config from master cat location : .kube/config  to here ie worker node at .kube)
ubuntu@ip-172-31-25-18:~/.kube$ sudo chmod 775 config

ubuntu@ip-172-31-25-18:~/.kube$ cd
ubuntu@ip-172-31-25-18:~$ kubectl get nodes
NAME              STATUS   ROLES           AGE   VERSION
ip-172-31-25-18   Ready    <none>          22m   v1.29.6
master            Ready    control-plane   70m   v1.29.6
