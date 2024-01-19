### Accès aux Machines Virtuelles

1. **Accès SSH aux VMs**:
   - Pour chaque VM, utilisez la commande `vagrant ssh <nom_vm>` depuis le répertoire où se trouve le `Vagrantfile` correspondant.
   - Par exemple, pour accéder au serveur d'application 1, utilisez `vagrant ssh app-server-1`.

2. **Accès Webmin**:
   - Webmin est accessible via le navigateur à l'adresse `http://<ip_vm>:10000`.
   - Par exemple, pour le serveur d'application 1, utilisez `http://192.168.56.2:10000`.

### Interagir avec Docker

1. **Lister les conteneurs Docker**:
   - `docker ps` pour voir tous les conteneurs en cours d'exécution.
   - `docker ps -a` pour voir tous les conteneurs, même ceux qui sont arrêtés.

2. **Accéder aux logs d'un conteneur**:
   - `docker logs <nom_conteneur>` pour voir les logs d'un conteneur spécifique.

3. **Exécuter des commandes à l'intérieur d'un conteneur**:
   - `docker exec -it <nom_conteneur> /bin/bash` pour obtenir un shell interactif à l'intérieur du conteneur.

### Configuration du Master Kubernetes

Pour le master Kubernetes, vous devrez installer et configurer Kubernetes après avoir installé Docker. Voici les étapes de base :

1. **Installation de Kubernetes**:
   - Installez kubeadm, kubelet, et kubectl en suivant la [documentation officielle](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).

2. **Initialisation du Cluster Kubernetes**:
   - Utilisez `sudo kubeadm init` pour initialiser le cluster.
   - Suivez les instructions à l'écran pour configurer votre utilisateur non root pour gérer le cluster.

3. **Installation du réseau Pod**:
   - Installez un plugin réseau, comme Calico ou Flannel. Par exemple, pour Calico, utilisez `kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml`.

4. **Joindre les Nœuds au Cluster**:
   - Utilisez la commande fournie par `kubeadm init` sur les nœuds pour les joindre au cluster.

5. **Vérifier l'état du cluster**:
   - Utilisez `kubectl get nodes` pour voir l'état des nœuds.

6. **Déployer des applications**:
   - Utilisez `kubectl apply -f <fichier_de_configuration>` pour déployer vos applications sur le cluster.

### Accès aux Applications

- **Serveurs d'Application**:
  - Vos applications seront accessibles via les adresses IP des serveurs d'application sur le port configuré (par défaut 80 pour le web).
  - Par exemple, `http://192.168.56.2` et `http://192.168.56.3`.

- **Serveur de Répartiteur de Charge (Nginx)**:
  - Si vous avez configuré Nginx pour répartir la charge entre vos applications, vous pouvez y accéder via l'adresse IP du répartiteur de charge.
  - Par exemple, `http://192.168.56.1`.

- **Serveurs de Base de Données**:
  - L'accès direct aux bases de données se fait généralement via des outils de gestion de base de données ou des applications backend, et non via un navigateur.
