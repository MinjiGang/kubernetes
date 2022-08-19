# Kubernetes / k8s ☸
<img src="https://github.com/kubernetes/kubernetes/raw/master/logo/logo.png" width="100">

----

Kubernetes, also known as K8s, is an open source system for managing [containerized applications]
across multiple hosts. It provides basic mechanisms for deployment, maintenance,
and scaling of applications.

Kubernetes builds upon a decade and a half of experience at Google running
production workloads at scale using a system called [Borg],
combined with best-of-breed ideas and practices from the community.

Kubernetes is hosted by the Cloud Native Computing Foundation ([CNCF]).
If your company wants to help shape the evolution of
technologies that are container-packaged, dynamically scheduled,
and microservices-oriented, consider joining the CNCF.
For details about who's involved and how Kubernetes plays a role,
read the CNCF [announcement].

----
[announcement]: https://cncf.io/news/announcement/2015/07/new-cloud-native-computing-foundation-drive-alignment-among-container
[Borg]: https://research.google.com/pubs/pub43438.html
[CNCF]: https://www.cncf.io/about
[containerized applications]: https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/

#### files that used in this project.
- namespace.yaml
- cluster.yaml
- deployment.yaml
- limit.yaml
- service.yaml
- ingress.yaml
- controller.yaml
- cwinsight.yaml
- scaler.yaml

### Software and Framework to be install and download.
- [eksctl](https://www.eksctl.io)
- [kubectl](https://kubernetes.io/docs/home/)
- [awscliv2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

##### userdata that was used in this project

```
#!/bin/bash
apt install -y unzip jq curl wget
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
curl -o kubectl https://s3.us-west-2.amazonaws.com/amazon-eks/1.23.7/2022-06-29/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/local/bin
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
cat <<EOF >>/etc/ssh/sshd_config
Port 2220
EOF
sed -i "/PasswordAuthentication/d" /etc/ssh/sshd_config
systemctl restart sshd
sudo echo -e "Skills2024**\nSkills2024**" | sudo passwd ubuntu
sudo echo -e "AKIA55IMW4OZ2EXYAWGQ\nC/nk7hmoQ7TIjtTWEvZ+tego+8+8Or3Ax3/scuwW\nap-northeast-2\njson" | aws configure
```
```
sudo echo -e "{password}\n{password}" | sudo passwd {user}
```

`-e` is to recognize `\n` as new line.

`sudo` is **root** access for Ubuntu.

The above command passes the password and a new line, two times, to `passwd`, which is what passwd requires.

If not using variables, I think this probably works.

##### example: (amazon linux 2)

```
sudo echo -e "Daeyang1@#\nDaeyang1@#" | sudo passwd ec2-user
```

## Manifest Files: Kubernetes Objects
#### - Cluster.yaml
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: worldskills-cloud-cluster
  region: ap-northeast-2
  version: "1.22"

vpc:
  id: vpc-0f8fd1f154d4c39c4
  subnets:
    private:
      worldskills-cloud-priv-sn-a:
        id: subnet-0c92699bef1d18422
      worldskills-cloud-priv-sn-c:
        id: subnet-091a1baf6da30ed33

managedNodeGroups:
  - name: worldskills-cloud-node
    instanceName: worldskills-cloud-node
    instanceType: t3.medium
    desiredCapacity: 2
    volumeSize: 80
    privateNetworking: true
    iam:
      withAddonPolicies:
        imageBuilder: true
        autoScaler: true
        albIngress: true
        cloudWatch: true
      attachPolicyARNs:
        - arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy
        - arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy
        - arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly
        - arn:aws:iam::aws:policy/ElasticLoadBalancingFullAccess
        - arn:aws:iam::aws:policy/AmazonS3FullAccess

fargateProfiles:
  - name: worldskills-cloud-ws-profile
    selectors:
      - namespace: worldskills-ns

secretsEncryption:
  keyARN: arn:aws:kms:ap-northeast-2:956193760179:key/d42fbb4c-6c77-4aab-af4f-478211c948f3
```
