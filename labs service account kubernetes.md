# Lab Kubernetes : ServiceAccount, RBAC et accès à l'API Server

## Objectif

Dans ce laboratoire, vous allez :

- Créer un ServiceAccount
- Créer un Role RBAC
- Associer le Role au ServiceAccount via un RoleBinding
- Déployer un Pod utilisant ce ServiceAccount
- Utiliser le token monté automatiquement dans le Pod
- Interroger l'API Kubernetes depuis le Pod
- Vérifier les permissions RBAC

---

## Architecture

```text
+------------------+
| Kubernetes API   |
| Server           |
+---------+--------+
          ^
          |
          | HTTPS + Token
          |
+---------+--------+
| Pod             |
| api-client      |
+---------+--------+
          |
          |
          v
+------------------+
| ServiceAccount   |
| api-reader       |
+------------------+
          |
          v
+------------------+
| Role             |
| Read Pods        |
+------------------+
```

---

# Étape 1 - Créer un ServiceAccount

Créer le fichier `serviceaccount.yaml` :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: api-reader
  namespace: default
```

Déployer le ServiceAccount :

```bash
kubectl apply -f serviceaccount.yaml
```

Vérifier sa création :

```bash
kubectl get serviceaccounts
```

Résultat attendu :

```text
NAME         SECRETS   AGE
api-reader   0         10s
default      0         5d
```

---

# Étape 2 - Créer un Role

Créer le fichier `role.yaml` :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default

rules:
- apiGroups: [""]
  resources:
  - pods
  verbs:
  - get
  - list
  - watch
```

Appliquer la configuration :

```bash
kubectl apply -f role.yaml
```

Vérifier :

```bash
kubectl get role
```

---

# Étape 3 - Créer un RoleBinding

Créer le fichier `rolebinding.yaml` :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default

subjects:
- kind: ServiceAccount
  name: api-reader
  namespace: default

roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-reader
```

Déployer :

```bash
kubectl apply -f rolebinding.yaml
```

Vérifier :

```bash
kubectl get rolebinding
```

---

# Étape 4 - Déployer un Pod utilisant le ServiceAccount

Créer le fichier `pod.yaml` :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api-client

spec:
  serviceAccountName: api-reader

  containers:
  - name: client
    image: curlimages/curl:latest

    command:
    - sleep
    - "3600"
```

Déployer :

```bash
kubectl apply -f pod.yaml
```

Vérifier :

```bash
kubectl get pod api-client
```

Résultat attendu :

```text
NAME         READY   STATUS    RESTARTS   AGE
api-client   1/1     Running   0          20s
```

---

# Étape 5 - Se connecter au Pod

```bash
kubectl exec -it api-client -- sh
```

---

# Étape 6 - Explorer le token du ServiceAccount

Lister les fichiers montés automatiquement :

```bash
ls /var/run/secrets/kubernetes.io/serviceaccount
```

Résultat attendu :

```text
ca.crt
namespace
token
```

Afficher le namespace :

```bash
cat /var/run/secrets/kubernetes.io/serviceaccount/namespace
```

Afficher le token :

```bash
cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

> Le token JWT permet au Pod de s'authentifier auprès de l'API Kubernetes.

---

# Étape 7 - Préparer les variables

Toujours dans le Pod :

```bash
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)

NAMESPACE=$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace)
```

Afficher les variables Kubernetes injectées :

```bash
echo $KUBERNETES_SERVICE_HOST
echo $KUBERNETES_SERVICE_PORT
```

Exemple :

```text
10.96.0.1
443
```

---

# Étape 8 - Interroger l'API Kubernetes

Lister les Pods du namespace :

```bash
curl -k \
-H "Authorization: Bearer $TOKEN" \
https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/${NAMESPACE}/pods
```

Résultat attendu :

```json
{
  "kind": "PodList",
  "items": [...]
}
```

---

# Étape 9 - Formater le résultat

Si jq est disponible :

```bash
curl -s -k \
-H "Authorization: Bearer $TOKEN" \
https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/${NAMESPACE}/pods \
| jq .
```

Afficher uniquement les noms des Pods :

```bash
curl -s -k \
-H "Authorization: Bearer $TOKEN" \
https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/${NAMESPACE}/pods \
| jq '.items[].metadata.name'
```

---

# Étape 10 - Vérifier les permissions RBAC

Depuis votre machine :

```bash
kubectl auth can-i list pods \
--as=system:serviceaccount:default:api-reader \
-n default
```

Résultat :

```text
yes
```

Vérifier une action interdite :

```bash
kubectl auth can-i list secrets \
--as=system:serviceaccount:default:api-reader \
-n default
```

Résultat :

```text
no
```

---

# Étape 11 - Tester une action interdite

Dans le Pod :

```bash
curl -k \
-H "Authorization: Bearer $TOKEN" \
https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/default/secrets
```

Résultat attendu :

```json
{
  "kind":"Status",
  "status":"Failure",
  "reason":"Forbidden"
}
```

Le ServiceAccount ne possède pas les permissions nécessaires.

---

# Bonus - Autoriser la création de Pods

Modifier le Role :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default

rules:
- apiGroups: [""]
  resources:
  - pods
  verbs:
  - get
  - list
  - watch
  - create
```

Appliquer :

```bash
kubectl apply -f role.yaml
```

---

# Bonus - Créer un Pod via l'API Kubernetes

Dans le Pod :

```bash
cat > pod.json <<EOF
{
  "apiVersion":"v1",
  "kind":"Pod",
  "metadata":{
    "name":"created-from-api"
  },
  "spec":{
    "containers":[
      {
        "name":"nginx",
        "image":"nginx"
      }
    ]
  }
}
EOF
```

Créer le Pod :

```bash
curl -k \
-X POST \
-H "Authorization: Bearer $TOKEN" \
-H "Content-Type: application/json" \
--data @pod.json \
https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/default/pods
```

Vérifier :

```bash
kubectl get pods
```

Résultat :

```text
NAME               READY   STATUS
api-client         1/1     Running
created-from-api   1/1     Running
```

---

# Questions de validation

1. Quel est le rôle d'un ServiceAccount ?
2. Où est stocké le token dans un Pod ?
3. Quelle ressource RBAC permet de définir des permissions ?
4. Quelle ressource RBAC permet d'associer des permissions à un utilisateur ou ServiceAccount ?
5. Pourquoi l'accès aux Secrets est-il refusé ?
6. Comment vérifier les permissions effectives d'un ServiceAccount ?
7. Quel est l'intérêt d'utiliser un ServiceAccount plutôt que le compte `default` ?

---

# Résumé

| Ressource | Rôle |
|------------|--------|
| ServiceAccount | Identité utilisée par les Pods |
| Role | Définit les permissions |
| RoleBinding | Associe les permissions à une identité |
| Token JWT | Authentifie le Pod auprès de l'API |
| API Server | Point d'entrée du cluster Kubernetes |
| kubectl auth can-i | Vérifie les permissions RBAC |

À la fin de ce laboratoire, vous êtes capable d'utiliser un ServiceAccount pour authentifier un Pod auprès de l'API Kubernetes et d'appliquer le principe du moindre privilège grâce au RBAC.
