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

IMPORTANT: notre configuration runner reste le même que celui dans l'installation
```toml
concurrent = 1
check_interval = 0
connection_max_age = "15m0s"
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "therunner"
  url = "http://gitlab.local:8082"
  token = "VOTRE_TOKKEN"
  executor = "docker"
  [runners.docker]
    tls_verify = false
    image = "docker:27"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    extra_hosts = [
      "harbor.host:192.168.88.116",
      "gitlab.local:192.168.88.116"
    ]
    # Important pour DIND stable
    volumes = [
      "/cache"
    ]
    network_mode = "bridge"
  [runners.cache]
```

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

## 2.6 Dépot gitlab
- Afin d'executer le job il suffit de le mettre sur gitlab
```bash
git clone <URL_HTTPS_du_dépôt>
cd <nom_du_dépôt>
git remote add origin <http://gitlab.local:8082/root/exemple-projet.git> (ici on utilise le lien vers notre projet gitlab)
git branch -M main
git push -uf origin main

```
- Une fois que le dépot est fini, normalement le job sera un succès

---


## 3. Workflow global

```
Un développeur push sur main, develop ou via une merge request

GitLab lance le build frontend + backend

Les images sont poussées dans Harbor (tag + latest)

Trivy télécharge et scanne les images

Résultat affiché dans GitLab CI/CD
