# Instalando un nodo de Kubernetes
## Documentacion oficial
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker
- https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
## Instalar los componentes necesarios con un script

Descargar el script con git en cada uno de los nodos:
```
git clone URL
```

Ejecutar en cada nodo (***Ubuntu***):
```
sh instala_docker_kube.sh
```

## Levantando un cluster de K8s con Calico

### Documentación official
Se puede seguir la guia oficial que se encuentra en: 
https://docs.projectcalico.org/getting-started/kubernetes/quickstart

### Create a single-host Kubernetes cluster

1. Lo mejor es seguir la guia de instalación oficial que se encuentra en https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/ o utilizar el script del repositorio

   > **Note**: Despues de instalar kueadm **no apagues o reinicies el host, continua directamente con el siguiente paso**.

2. Si estas como usuario normal are una terminal en el host donde has instalado kueadm con sudo.
3. Inicializa el nodo master con el siguiente comando:

   ```
   sudo kubeadm init --pod-network-cidr=192.168.0.0/16
   ```

   > **Nota**: Si ya está en uso la red 192.168.0.0/16 o quieres usar otra, debes selecionar otro direccionamiento para pods replazandolo en el comando anterior.

4. Ejecuta los siguientes comandos para configurar kubectl, son los mismos que devuelve `kubeadm init`.

   ```
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   export KUBECONFIG=/etc/kubernetes/admin.conf
   ```

###  Instalando Calico

1. Instala el operado Tigera Calico y los recursos personalizados.
   ```
   kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
   ```

2. Instala Calico creando los recursos personalizados necesarios. Si quieres mas opciones del manifiesto visita https://docs.projectcalico.org/reference/installation/api.

   ```
   kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
   ```

   > **Nota**: Si se a modificado el direccionamiento y no es 192.168.0.0/24 lo mejor es descargar el fichero https://docs.projectcalico.org/manifests/custom-resources.yaml  editarlo para que coincida con el direccionamiento y aplicar:

   ```
   kubectl -f custom-resources.yml
   ```

3. Confirma que todos los pods están corriendo con el siguiente comando:

   ```
   watch kubectl get pods -n calico-system
   ```
   >***Nota:*** En algunas instalaciones ha de hacerse con:
```
   watch kubectl get pods -n kube-system
```
   Hay que esperar hasta que cada pod tenga `STATUS` of `Running`.



4. Elimina los nodos contaminados:

   ```
   kubectl taint nodes --all node-role.kubernetes.io/master-
   ```

   Esto debe retornar:

   ```
   node/<your-hostname> untainted
   ```

5. Puedes añadir nodos al cluster y luego revisar su estado con:

   ```
   kubectl get nodes -o wide
   ```

   Esto debe retornar algo así:

   ```
   NAME              STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
   <your-hostname>   Ready    master   52m   v1.12.2   10.128.0.28   <none>        Ubuntu 18.04.1 LTS   4.15.0-1023-gcp   docker://18.6.1
   ```


