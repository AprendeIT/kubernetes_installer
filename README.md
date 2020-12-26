https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/



https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker

https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd



https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/



https://docs.projectcalico.org/getting-started/kubernetes/quickstart



####  Instalando Calico

1. Install the Tigera Calico operator and custom resource definitions.

   ```
   kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
   ```

2. Install Calico by creating the necessary custom resource. For more information on configuration options available in this manifest, see [the installation reference](https://docs.projectcalico.org/reference/installation/api).

   ```
   kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
   ```

   > **Note**: Before creating this manifest, read its contents and make sure its settings are correct for your environment. For example, you may need to change the default IP pool CIDR to match your pod network CIDR.

   Si se a modificado el direccionamiento y no es 192.168.0.0/24 lo mejor es descargar el fichero https://docs.projectcalico.org/manifests/custom-resources.yaml  editarlo para que cincida con el direccionamiento y aplicar:

   ```
   kubectl -f custom-resources.yml
   ```

   

3. Confirm that all of the pods are running with the following command.

   ```
   watch kubectl get pods -n calico-system
   ```

   Wait until each pod has the `STATUS` of `Running`.

   > **Note**: The Tigera operator installs resources in the `calico-system` namespace. Other install methods may use the `kube-system` namespace instead.

4. Remove the taints on the master so that you can schedule pods on it.

   ```
   kubectl taint nodes --all node-role.kubernetes.io/master-
   ```

   It should return the following.

   ```
   node/<your-hostname> untainted
   ```

5. Confirm that you now have a node in your cluster with the following command.

   ```
   kubectl get nodes -o wide
   ```

   It should return something like the following.

   ```
   NAME              STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
   <your-hostname>   Ready    master   52m   v1.12.2   10.128.0.28   <none>        Ubuntu 18.04.1 LTS   4.15.0-1023-gcp   docker://18.6.1
   ```

Congratulations! You now have a single-host Kubernetes cluster with Calico.