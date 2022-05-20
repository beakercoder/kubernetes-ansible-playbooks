It's reasonably straightforward to build you Kubernetes cluster once you have all the requirements in an Ansible Playbook. With a few settings, it's essentially "click and go".

The playbooks are supplied here, but assume the following:
-You have two or more Linux (Ubuntu/Debian) computers (or VM's) running to install Kubernetes that meet the requirements of Kubernetes. (Kubernetes will also run on Windows but that's not covered in this document.) SSH port 22 needs to be enabled.
-Ansible is installed on each. See easy install here. A new user must be added, ansadmin, that is part of the sudoers group.
-If on an AWS instance, password authentication must be enabled. (edit /etc/ssh/sshd_config and change setting from no to yes)
-SSH keys need to be generated and copied to the machines.

At this point the scripts are ready to be executed, provided you supply some specifics about your environment. 
These three scripts will install Kubernetes on a Control Plane and Workers. It suggests (and provides steps) to install Calico, and uses CRIO-Docker as a container engine.

One of the nice things about the scripts is that they are repeatable. If you're like me you often tear down and start over.

The first script (k8s-prereqs-dockerd-crio.yaml) should be ran against all Worker and Control Plane nodes. 
It performs the following tasks:
-Opens ports needed by Kubernetes and Calico. (6443, 32750, 10250, 4443, 8080, 179)
-Disables swap
-Installs Docker and all needed packages
-Installs Go
-Install Containerd
-Builds dockerd
-Initializes and enabled cri-docker.sock

The second script (k8s-kubectl-kubelet-kubeadm) should be ran against all Worker and Control Plane nodes.
It performs the following tasks:
-Installs kubectl
-Installs kubeadm
-Install kubelet
-sets installs to HOLD

The third (k8s-createcluster.yaml) script should be ran only against the Control Plane nodes. 
-It performs the following tasks:
-sets up containerd
-Initialized the cluster

Once complete, run the following command on the Control Plane to get the join command for the worker nodes.
kubeadm token create - - print-join-command
You should now have a working Kubernetes cluster with repeatable Ansible scripts specific to your environment.