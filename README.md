# 🚀 Déploiement GitHub Pages avec Ansible + GitHub Actions

Ce guide explique pas à pas comment mettre en place un dépôt GitHub qui déploie automatiquement un site statique sur **GitHub Pages** grâce à **Ansible** et **GitHub Actions**.

* * *

## 🛠️ Prérequis

- Un compte GitHub
    
- [Git](https://git-scm.com/) installé sur ta machine locale
    
- GitHub Pages activé dans **Settings > Pages** du dépôt (sur la branche `gh-pages`)
    
- Aucun secret à configurer (on utilise le `GITHUB_TOKEN` fourni automatiquement)
    

* * *

## 📝 Étapes détaillées

### 1\. Créer un nouveau projet local

`# Créer un nouveau dossier`

`mkdir mon-projet`

`cd mon-projet`

`# Initialiser Git`

`git init`

`# Créer un premier commit vide`

`git commit --allow-empty -m "Initial commit"`

* * *

### 2\. Créer le dépôt GitHub distant

Sur GitHub :

- Clique sur **New Repository**
    
- Donne-lui un nom (ex: `ansible-ghpages-demo`)
    
- Ne coche **aucune case d’initialisation** (README, licence, .gitignore)
    

Puis connecte ton dépôt local au dépôt distant :

`git remote add origin https://github.com/<utilisateur>/<repo>.git`

`git branch -M main`

`git push -u origin main`

* * *

### 3\. Créer la structure du projet

`mkdir -p .github/workflows`

`mkdir templates`

`touch deploy.yml`

`touch templates/index.html.j2`

`touch .github/workflows/deploy.yml`

* * *

### 4\. Ajouter le playbook Ansible (`deploy.yml`)
`- name: Deploy site to GitHub Pages
  hosts: localhost
  connection: local
  tasks:
    - name: Create site directory
      file:
        path: site
        state: directory
    - name: Generate index.html from template
      template:
        src: templates/index.html.j2
        dest: site/index.html
      vars:
        repo_url: "https://github.com/{{ lookup('env','GITHUB_REPOSITORY') }}"
        pages_url: "https://{{ lookup('env','GITHUB_REPOSITORY_OWNER') }}.github.io/{{ lookup('env','GITHUB_REPOSITORY').split('/')[-1] }}"
        commit_sha: "{{ lookup('env','GITHUB_SHA') }}"
        build_date: "{{ lookup('pipe','date') }}"`

* * *

### 5\. Créer le template HTML (`templates/index.html.j2`)

<!DOCTYPE html>
<html>
<head>
    <title>🚀 Déploiement GitHub Pages</title>
</head>
<body>
    <h1>🚀 Déploiement GitHub Pages avec Ansible</h1>
    <p>📅 Déployé le : {{ build_date }}</p>
    <p>🔖 Commit : {{ commit_sha }}</p>
    <p>🔗 <a href="{{ repo_url }}">Lien vers le dépôt GitHub</a></p>
    <p>🌍 <a href="{{ pages_url }}">Lien vers la page GitHub Pages</a></p>
    <img src="https://media.giphy.com/media/13HgwGsXF0aiGY/giphy.gif" alt="fun image" />
</body>
</html>


* * *

### 6\. Créer le workflow GitHub Actions (`.github/workflows/deploy.yml`)

`name: Deploy to GitHub Pages
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'
      - name: Install Ansible
        run: pip install ansible
      - name: Run Ansible playbook
        run: ansible-playbook deploy.yml
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site`

* * *

### 7\. Ajouter et pousser le code

`git add .`

`git commit -m "Ajout du workflow GitHub Actions et du playbook Ansible"`

`git push origin main`

* * *

### 8\. Activer GitHub Pages

- Aller dans **Settings > Pages**
    
- Sélectionner la branche `gh-pages`
    
- Sauvegarder
    

* * *

### 9\. Vérifier le déploiement

Une fois le workflow terminé, ton site est disponible à l’adresse :

👉 `https://<utilisateur>.github.io/<repo>/`

* * *

## ✅ Résultat attendu

La page générée contient :

- La date et l’heure du déploiement
    
- Le hash du commit déployé
    
- Un lien vers le dépôt GitHub
    
- Un lien vers la page GitHub Pages
    
- Une image fun 😎