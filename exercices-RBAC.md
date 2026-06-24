Exercice RBAC – Gestion des applications dans un environnement d'entreprise
Contexte

L'entreprise TechCorp dispose d'un cluster Kubernetes utilisé par plusieurs équipes.

L'équipe Application Delivery est responsable du déploiement et de la maintenance des applications dans le namespace production.

Pour des raisons de sécurité, les membres de cette équipe ne doivent disposer que des droits strictement nécessaires à leurs activités quotidiennes.

Un nouvel utilisateur, jdupont, rejoint l'équipe et doit recevoir les permissions adaptées.

Besoin métier

L'utilisateur jdupont doit pouvoir :

Sur les Pods
Consulter les Pods
Créer des Pods
Modifier des Pods existants
Consulter les logs des Pods
Sur les Deployments
Consulter les Deployments
Créer des Deployments
Modifier des Deployments
Sur les StatefulSets
Consulter les StatefulSets
Créer des StatefulSets
Modifier des StatefulSets
Sur les CronJobs
Consulter les CronJobs
Créer des CronJobs
Modifier des CronJobs
Contraintes de sécurité

L'utilisateur ne doit pas pouvoir :

Supprimer des ressources
Consulter les Secrets
Créer ou modifier des Roles
Créer ou modifier des RoleBindings
Créer ou modifier des ClusterRoles
Créer ou modifier des ClusterRoleBindings
Accéder aux ressources d'autres namespaces
Travaux demandés
Partie 1 : Analyse des besoins

À partir des exigences ci-dessus :

Identifier les API Groups concernés.
Identifier les ressources Kubernetes concernées.
Identifier les verbes RBAC nécessaires.
Déterminer si un Role ou un ClusterRole est le plus adapté.
Justifier votre choix.
Partie 2 : Création des objets RBAC

Créer les objets Kubernetes nécessaires afin que :

Les permissions soient limitées au namespace production.
L'utilisateur jdupont dispose uniquement des droits décrits dans le besoin métier.
Les contraintes de sécurité soient respectées.

Vous devrez produire :

Le manifeste du rôle RBAC.
Le manifeste permettant d'associer ce rôle à l'utilisateur.
Partie 3 : Vérification des permissions

Écrire les commandes permettant de vérifier que l'utilisateur possède les droits nécessaires.

Vérifier notamment les actions suivantes :

Pods
Lire les Pods
Créer un Pod
Modifier un Pod
Consulter les logs d'un Pod
Deployments
Lire les Deployments
Créer un Deployment
Modifier un Deployment
StatefulSets
Lire les StatefulSets
Créer un StatefulSet
Modifier un StatefulSet
CronJobs
Lire les CronJobs
Créer un CronJob
Modifier un CronJob
Partie 4 : Vérification des restrictions

Écrire les commandes permettant de vérifier que les actions suivantes sont interdites :

Administration RBAC
Créer un Role
Modifier un Role
Créer un RoleBinding
Modifier un RoleBinding
Secrets
Lire un Secret
Lister les Secrets
Suppression
Supprimer un Pod
Supprimer un Deployment
Supprimer un StatefulSet
Supprimer un CronJob
Partie 5 : Validation opérationnelle

L'administrateur souhaite vérifier le comportement réel de l'utilisateur.

Décrire les étapes permettant de :

Se connecter en tant que jdupont.
Déployer une application de test.
Modifier cette application.
Consulter les logs associés.
Vérifier qu'il est impossible d'accéder à un Secret.
Vérifier qu'il est impossible de supprimer le Deployment.
Vérifier qu'il est impossible de créer un nouvel objet RBAC.
Questions de réflexion
Pourquoi est-il dangereux d'attribuer le rôle cluster-admin à tous les utilisateurs ?
Quelle différence existe-t-il entre un Role et un ClusterRole ?
Quelle différence existe-t-il entre les verbes update et patch ?
Pourquoi l'accès aux logs doit-il être explicitement autorisé dans RBAC ?
Quel principe de sécurité est appliqué lorsqu'on n'accorde que les permissions strictement nécessaires à un utilisateur ?
Quels risques pourraient apparaître si un développeur obtenait un accès en lecture aux Secrets du namespace production ?
Dans quel cas serait-il pertinent d'utiliser un ClusterRole pour cette équipe ?
Des réponses plus intelligentes, le chargement de fichiers et d’images, et bien plus encore.
Se connecter
Inscription gratuite


