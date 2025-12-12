# CI/CD GitLab → Harbor

## Déploiement d'images Docker (Frontend & Backend) + Scan Trivy

# 1. Structure du projet
```
project/
│── .gitlab-ci.yml
│
├── frontend/
│     ├── Dockerfile
│     └── src/
│           ├── index.html
│           └── app.js
│
└── backend/
      ├── Dockerfile
      ├── go.mod
      └── src/
            └── main.go
```
---

# 2. Prérequis

## 2.1 GitLab local fonctionnel
  
- GitLab doit être opérationnelle (HTTP)
  
## 2.2 Harbor installé et accessible
- Accéssible via:
```
http://harbor.host:8086
```
- Nom du projet: mardi
  
## 2.3 Autoriser GitLab à communiquer avec Harbor (important)
  
Dans GitLab Admin Area:
```
Admin Area → Settings → Network → Outbound Requests
    
Cocher :
      - Allow requests to the local network from webhooks and integrations
      - Allow requests to the local network from system hooks
      - Allow requests to local addresses
```
  
## 2.4 Intégration GitLab et Harbor
  
- Dans le projet GitLab :
```bash
    Settings - Integrations - Harbor
    
    Configurer :
      - Harbor URL : http://harbor.host:8086
      - Project : mardi
      - Username : admin
      - Password : Harbor12345
```

## 2.5 Les variables CI/CD

- Dans GitLab → Settings → CI/CD → Variables.
```bash
| Nom               | Valeur              |
| ----------------- | ------------------- |
| `HARBOR_REGISTRY` | `harbor.host:8086`  |
| `HARBOR_PROJECT`  | `mardi`             |
| `HARBOR_USERNAME` | utilisateur Harbor  |
| `HARBOR_PASSWORD` | mot de passe Harbor |
```

---


## 3. Workflow global

```
Un développeur push sur main, develop ou via une merge request

GitLab lance le build frontend + backend

Les images sont poussées dans Harbor (tag + latest)

Trivy télécharge et scanne les images

Résultat affiché dans GitLab CI/CD
