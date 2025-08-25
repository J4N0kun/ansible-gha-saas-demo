# 🚀 Procédure pas-à-pas : déploiement GitHub Pages avec Ansible & GHA

## 1\. Créer un nouveau dépôt Git

```bash
# Crée un dossier projet
mkdir ansible-gha-saas-demo
cd ansible-gha-saas-demo

# Initialise git
git init

# Ajoute un fichier README
echo "# Demo GitHub Pages avec Ansible" > README.md

# Crée le commit initial
git add .
git commit -m "Initial commit"

```

* * *

## 2\. Créer la structure du projet

```bash
mkdir -p templates .github/workflows
touch deploy.yml

```

Arborescence attendue :

```pgsql
ansible-gha-saas-demo/
├── deploy.yml
├── templates/
│   └── index.j2
├── .github/
│   └── workflows/
│       └── deploy.yml
└── README.md
```

3\. Écrire le playbook Ansible

Fichier **`deploy.yml`** :

```yaml
---
- name: Déploiement d’un site statique avec GitHub Pages
  hosts: localhost
  gather_facts: false
  vars:
    github_user: "{{ lookup('env','GITHUB_USER') | default('J4N0kun') }}"
    github_repo: "{{ lookup('env','GITHUB_REPOSITORY') | default('J4N0kun/ansible-gha-saas-demo') }}"
    site_dir: site
    image_url: "https://preview.redd.it/oynqiuq4a9k51.jpg?width=640&crop=smart&auto=webp&s=fb64945028dd789dc71031040a114f95025e65d7"

  tasks:
    - name: Créer le dossier site s'il n'existe pas
      file:
        path: "{{ site_dir }}"
        state: directory

    - name: Récupérer le commit Git courant
      command: git rev-parse --short HEAD
      register: git_commit

    - name: Générer la date courante
      command: date "+%d/%m/%Y %H:%M:%S"
      register: current_date

    - name: Générer le fichier HTML via template Jinja2
      template:
        src: templates/index.j2
        dest: "{{ site_dir }}/index.html"
      vars:
        commit: "{{ git_commit.stdout }}"
        date: "{{ current_date.stdout }}"
        github_url: "https://github.com/{{ github_repo }}"

    - name: Ajouter un fichier .nojekyll
      copy:
        dest: "{{ site_dir }}/.nojekyll"
        content: ""

```

* * *

## 4\. Créer le template Jinja2

Fichier **`templates/index.j2`** :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Demo GitHub Pages avec Ansible</title>
</head>
<body>
  <h1>Bienvenue sur le site statique 🎉</h1>
  <p>Dernier déploiement : {{ date }}</p>
  <p>Commit : {{ commit }}</p>
  <p>Repo GitHub : <a href="{{ github_url }}">{{ github_url }}</a></p>
  <img src="{{ image_url }}" alt="Image de démo" style="max-width:400px;">
</body>
</html>

```

* * *

## 5\. Créer le workflow GitHub Actions

Fichier **`.github/workflows/deploy.yml`** :

```yaml
name: Déploiement GitHub Pages avec Ansible

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.x"

      - name: Installer Ansible
        run: |
          python -m pip install --upgrade pip
          pip install ansible

      - name: Exécuter le playbook
        run: ansible-playbook -i inventory.ini deploy.yml

      - name: Déployer sur GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages

```

* * *

## 6\. Publier le dépôt sur GitHub

1.  Crée un dépôt vide sur [GitHub](https://github.com/new).  
    Exemple : `J4N0kun/ansible-gha-saas-demo`.
    
2.  Associe ton dépôt local :
    

```bash
git remote add origin git@github.com:J4N0kun/ansible-gha-saas-demo.git
git branch -M main
git push -u origin main

```

* * *

## 7\. Activer GitHub Pages

1.  Va dans **Settings → Pages** de ton dépôt GitHub.
    
2.  Choisis la branche **`gh-pages`** et `/ (root)` comme source.
    
3.  Clique **Save**.
    
4.  Ton site sera disponible à l’URL :
    
    `https://<ton_user>.github.io/ansible-gha-saas-demo/`
    

* * *

## 8\. Vérifier le résultat

- Chaque **push sur `main`** → déclenche le workflow.
    
- Le site est régénéré par **Ansible**.
    
- Les fichiers HTML sont publiés sur la branche **`gh-pages`**.
