### 1. Réseau Docker

Docker fournit un réseau par défaut où les conteneurs peuvent communiquer entre eux via des adresses IP internes. Pour une communication plus avancée, vous pouvez créer un réseau personnalisé :

1. **Créer un réseau Docker**:
   - Utilisez `docker network create <nom_du_reseau>` pour créer un réseau.
   - Par exemple, `docker network create mon_reseau`.

2. **Connecter les conteneurs au réseau**:
   - Lorsque vous exécutez un conteneur, utilisez l'option `--network=<nom_du_reseau>` pour le connecter au réseau.
   - Par exemple, `docker run --network=mon_reseau ...`.

3. **Communication entre les conteneurs**:
   - Les conteneurs sur le même réseau Docker peuvent communiquer entre eux en utilisant le nom du conteneur comme nom d'hôte.

### 2. Réseau Kubernetes

Kubernetes gère son propre réseau pour les pods et les services. Voici comment configurer la communication :

1. **Services Kubernetes**:
   - Créez des services Kubernetes pour exposer vos applications et bases de données. Cela permettra aux autres pods de communiquer avec eux en utilisant le nom du service.
   - Utilisez `kubectl expose ...` ou un fichier YAML de service pour exposer vos pods.

2. **Découverte de Service**:
   - Kubernetes utilise DNS pour la découverte de service. Cela signifie que vos applications peuvent communiquer avec d'autres services en utilisant le nom du service.

3. **Ingress ou LoadBalancer**:
   - Pour exposer vos services à l'extérieur du cluster Kubernetes, utilisez un Ingress ou un service de type LoadBalancer.

### 3. Configuration de la Base de Données

1. **Variables d'Environnement**:
   - Configurez vos applications pour se connecter à la base de données en utilisant des variables d'environnement pour l'adresse, le nom d'utilisateur, et le mot de passe de la base de données.
   - Par exemple, dans votre application, utilisez `DB_HOST`, `DB_USER`, et `DB_PASS` comme variables d'environnement.

2. **Configuration du Conteneur de la Base de Données**:
   - Assurez-vous que le conteneur de la base de données est accessible aux autres conteneurs. Si vous utilisez Docker, cela peut être configuré via des réseaux Docker. Si vous utilisez Kubernetes, cela sera géré via des services.

### 4. Sécurité et Bonnes Pratiques

- **Réseaux Isolés**:
  - Utilisez des réseaux isolés pour séparer différents environnements (par exemple, front-end, back-end, base de données).

- **Politiques de Réseau**:
  - Utilisez des politiques de réseau dans Kubernetes pour contrôler le trafic entre les pods.

- **Gestion des Secrets**:
  - Utilisez des mécanismes de gestion des secrets pour stocker des informations sensibles comme les mots de passe de la base de données.


Dans le contexte de l'infrastructure décrite, voici les commandes associées à la configuration et la gestion de la communication entre les conteneurs Docker, les services Kubernetes, et la base de données. Ces commandes sont à exécuter sur les machines virtuelles appropriées après vous y être connecté via SSH (avec `vagrant ssh <nom_vm>`).

### Docker

1. **Création d'un réseau Docker** (sur les serveurs d'application et de base de données) : 

docker network create mon_reseau
   

2. **Exécution de conteneurs avec connexion au réseau** (exemple pour une application web) : 

docker run --network=mon_reseau --name mon_app -d mon_app_image
   

3. **Exécution de conteneurs MySQL avec connexion au réseau** (exemple pour un serveur de base de données) : 

docker run --network=mon_reseau --name mon_mysql -e MYSQL_ROOT_PASSWORD=mypass -d mysql:5.7

### Docker précisions

1. **mon_reseau** : C'est le nom du réseau Docker que vous créez. Vous pouvez donner le nom que vous voulez. Ce réseau permettra aux conteneurs Docker de communiquer entre eux. Par exemple, vous pourriez nommer votre réseau `backend_network` ou `app_network`.

   docker network create backend_network

2. **mon_app** : C'est le nom que vous donnez à votre conteneur lorsque vous le démarrez. Ce nom peut être utilisé pour référencer le conteneur dans des commandes Docker, comme pour voir les logs, arrêter le conteneur, etc. Par exemple, si vous avez une application web, vous pourriez l'appeler `web_app`.

   docker run --network=backend_network --name web_app -d mon_app_image

3. **mon_app_image** : C'est le nom de l'image Docker à partir de laquelle votre conteneur est créé. Cela peut être le nom d'une image que vous avez construite localement (par exemple, à partir d'un Dockerfile) ou une image que vous avez tirée d'un registre d'images comme Docker Hub. Par exemple, si vous avez une image pour votre application web, vous pourriez l'appeler `my_web_app_image`.

   docker build -t my_web_app_image .

Et ensuite, vous pouvez utiliser cette image pour démarrer un conteneur :

   docker run --network=backend_network --name web_app -d my_web_app_image

Dans ces exemples, `backend_network`, `web_app`, et `my_web_app_image` sont des noms que j'ai choisis. Vous devriez les remplacer par des noms qui ont du sens dans le contexte de votre application et de votre infrastructure. 

### Kubernetes

1. **Initialisation du Cluster Kubernetes** (sur le master Kubernetes) :
   
   sudo kubeadm init --pod-network-cidr=192.168.0.0/16
   
2. **Installation du réseau Pod** (sur le master Kubernetes) :
   
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   
3. **Joindre les Nœuds au Cluster** (sur chaque nœud) :
   
   sudo kubeadm join <master_ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
   
4. **Exposition des applications via des services Kubernetes** (sur le master Kubernetes) :
   
   kubectl expose deployment mon_app --type=LoadBalancer --name=mon-service-app
   
5. **Création d'un Ingress pour exposer les services** (sur le master Kubernetes) :
   yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: mon-ingress
   spec:
     rules:
     - host: monapp.exemple.com
       http:
         paths:
         - path: /
           pathType: Prefix
           backend:
             service:
               name: mon-service-app
               port:
                 number: 80
   
   Appliquez avec :
   
   kubectl apply -f mon-ingress.yaml
   
### Gestion des Secrets (Kubernetes)

1. **Création d'un secret pour les informations de la base de données** (sur le master Kubernetes) :
   
   kubectl create secret generic db-secret --from-literal=username=dbuser --from-literal=password=dbpass
   
### Politiques de Réseau (Kubernetes)

1. **Création d'une politique de réseau** (sur le master Kubernetes) :
   yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: db-network-policy
   spec:
     podSelector:
       matchLabels:
         role: db
     policyTypes:
     - Ingress
     ingress:
     - from:
       - podSelector:
           matchLabels:
             role: app
   
   Appliquez avec :
   
   kubectl apply -f db-network-policy.yaml
   

Ces commandes sont des exemples basiques pour illustrer comment configurer la communication dans votre infrastructure. Vous devrez les adapter en fonction de votre configuration spécifique, des noms de vos images Docker, des labels de vos pods Kubernetes, et d'autres paramètres spécifiques à votre environnement. Assurez-vous de bien comprendre chaque commande et de tester votre configuration dans un environnement de développement avant de la déployer en production.
