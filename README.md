### Start Google Cloud Docker Container, Auth and Create Cluster:

```shell
$ sudo docker run -it --rm -v ${PWD}:/work -w /work --entrypoint /bin/bash google/cloud-sdk:160.0.0
```

```shell
$ cd /work
$ gcloud auth login
```

Copy the link given by the command in your browser.

Then, after authentication, copy the link provided by google in the shell.

---

After that, set your project ID where you will make your changes (if you doesn't have any project or want to create a new one, type *gcloud projects create <NAME_OF_THE_PROJECT>*)

```shell
$ gcloud config set project <PROJECT_ID>
``` 
Check K8s versions for region

```shell
$ gcloud container get-server-config --zone southamerica-east1-a
```

---

```shell
$ gcloud container clusters create <NAME_OF_THE_PROJECT> \
--cluster-version 1.18.16-gke.300 \
--disk-size 10 \
--num-nodes 1 \
--machine-type e2-small \
--no-enable-cloud-endpoints \
--no-enable-cloud-logging \
--no-enable-cloud-monitoring \
--zone southamerica-east1-a
```

---

After the cluster is up, install **kubectl** and get kubeconfig for your cluster:

```shell
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Validate the binary with checksum file:

```shell
$ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

$ echo "$(<kubectl.sha256) kubectl" | sha256sum --check
```

Install kubectl:

```shell
$ install -o root -g root -m 0755 kubectl /usr/bin/kubectl
```

Check if **client** are within one minor version difference of your **cluster** (**server**).

```shell
$ kubectl version --short
Client Version: v1.6.4
Server Version: v1.18.16-gke.300
```

If not, remove and download the proper version (or downgrade your server).
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

---

Fetching the kubeconfig to our cluster:

```shell
gcloud container clusters get-credentials <NAME_OF_THE_PROJECT> --zone southamerica-east1-a
```

---

### Generate Configmap to custom /usr/share/nginx/html/index.html

```shell
$ kubectl create configmap nginx-index-html-configmap --from-file=index.html -o yaml --dry-run=client
```

### Clean up! (or get poor)

```shell
$ gcloud container clusters delete <NAME_OF_THE_PROJECT> --zone southamerica-east1-a
```