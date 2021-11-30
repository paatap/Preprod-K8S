# Preprod-K8S
Company preprod k8s clister On Debian 10

-- 
  apt update 
  apt install net-tools
  apt-get update
  apt-get install -y apt-transport-https ca-certificates curl
--  Add To /etc/hosts file 

172.17.112.225	iot-preprod-vm1	kube-master-preprod	kmaster-1
172.17.112.226	kmaster-2
172.17.112.227	knode-1
172.17.112.228	knode-2
172.17.112.229	knode-3

  apt install mc
  --  Add K8S sources list in apt repos  
 curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
 echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" |   tee /etc/apt/sources.list.d/kubernetes.list
  apt update
  apt install docker.io

 systemctl  status docker
 ping kube-master-preprod
 apt-get install -y kubelet kubeadm kubectl

-- check cgroup drivers for docker
 
 systemctl status docker
 
-- check kubelet service status

 systemctl status kubelet
 
 -- Change docker cgroup driver
 
 -- Create daemon.json config file
   59  docker system info | grep -i driver
   60  cat <<EOF > /etc/docker/daemon.json
   61  {
   62    "exec-opts": ["native.cgroupdriver=systemd"]
   63  }
   64  EOF
  
 Check crated file
  
  cat /etc/docker/daemon.json 
  
 --Change docker cgroup driver
  
 systemctl daemon-reload
 systemctl stop docker
 systemctl start docker
 docker system info | grep -i driver
  
  
 -- If kubeadm was lunched with problems

 docker system info | grep -i driver
  
 rm -rf /var/lib/etcd/ /var/lib/cni /var/run/kubernetes /etc/kubernetes
  
systemctl daemon-reload
  
kubeadm reset
  
systemctl daemon-reload
  
kubeadm reset
  
systemctl stop docker
  
systemctl start docker

  
  -- Initialise k8s cluster
  
  
-- kubeadm init --control-plane-endpoint kube-master-preprod:6443 --upload-certs --pod-network-cidr 192.168.0.0/24 --upload-certs
  
 -- After sucess 
  
 --  To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

-- Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

/* You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/   */

--  You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join kube-master-preprod:6443 --token j1bscz.wuwg4lxj4zj5vk68 --discovery-token-ca-cert-hash sha256:5fb60694c7213f0c00300267c877995708558dc73afcb0360c4586cf4b988f4d --control-plane --certificate-key 897cb38da22a866c3a6b0cbf91005a86d7fe53d2a642a41c621e0e0eeceda7f0 

/* Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:   */

kubeadm join kube-master-preprod:6443 --token j1bscz.wuwg4lxj4zj5vk68 --discovery-token-ca-cert-hash sha256:5fb60694c7213f0c00300267c877995708558dc73afcb0360c4586cf4b988f4d 

  
  -- If Propblems Check Kubelet logs
  
   journalctl -u kubelet
  
  -- Check k8s nods ... 
  
   kubectl get nodes  
  
  -- Deploy Calico Pod Network Add-on (Master Node)
  
  kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
  
 kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml

  
  
  
 
