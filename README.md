kubernetes - public guidelines
sudo su

1. Prequisites
01 master server (for single-master cluster) with xxx.xxx.xxx.xxx (IP of master)
02 nodes.
For simlifying process => all with Xenial


2. Docker 17.03
apt-get update && apt-get install -y curl apt-transport-https
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/docker.list
deb https://download.docker.com/linux/$(lsb_release -si | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable
EOF
apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 17.03 | head -1 | awk '{print $3}')

—— In cases you want save docker data to another place ——
cat <<EOF >/etc/docker/daemon.json
{
"graph":"/mnt/vol-sgp/docker-data"
}
EOF
systemctl restart docker
—————————————

2. install kubeadm
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update
apt-get install -y kubelet kubeadm kubectl kubernetes-cni

———— init cluster using Calico ————
kubeadm init --pod-network-cidr=192.168.0.0/16
kubectl apply -f https://docs.projectcalico.org/v2.6/getting-started/kubernetes/installation/hosted/kubeadm/1.6/calico.yaml

export KUBECONFIG=/etc/kubernetes/admin.conf
# copy this to ~/.bashrc if you want to automatic add VAR when login

kubectl token create
#write down this token for NODE joining - eg => ttto123.kennnnn1231213313

2.a dashboard - skip this with don’t want dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

3. kube-cluster
# please install Docker as guideline above

—-> docker + kubeadm
kubeadm join --token ttto123.kennnnn1231213313 xxx.xxx.xxx.xxx:6443
