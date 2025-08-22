# **Procédure POC Ansible + GitHub Actions + GitHub Pages**

## **Pré-requis**

Avant de commencer, assure-toi de disposer de :

1.  **Un compte GitHub** et d’un dépôt pour le projet.
    
2.  **Git installé** sur ta machine locale.
    
3.  **Python et Ansible** si tu souhaites tester le playbook localement.
    
4.  **GitHub Actions activé** sur le dépôt.
    
5.  **GitHub Pages activé** :
    
    - Branche : `gh-pages`
        
    - Source : `/ (root)`
        
6.  **Secrets GitHub** configurés :
    
    - `GITHUB_TOKEN` (fourni automatiquement par GitHub Actions, utilisé pour pousser sur `gh-pages`).
7.  Facultatif : **ngrok** si tu veux exposer ton site localement pour test avant déploiement.
    

* * *

## **1️⃣ Créer et initialiser le dépôt Git**

1.  Crée un dossier pour ton projet et place-toi dedans :

`mkdir ansible-gha-saas-democd ansible-gha-saas-demo`

2.  Initialise le dépôt Git et ajoute l’URL de ton dépôt GitHub (remplace `<utilisateur>` par ton nom GitHub) :

`git initgit remote add origin https://github.com/<utilisateur>/ansible-gha-saas-demo.git`

* * *

## **2️⃣ Créer l’inventaire Ansible**

Pour un POC local, crée un fichier `inventory.ini` :

`[local]localhost ansible_connection=local`

> Si aucun inventaire n’est fourni, Ansible utilisera `localhost` par défaut. Les warnings *No inventory was parsed* et *provided hosts list is empty* sont normaux.

* * *

## **3️⃣ Créer le playbook Ansible**

Crée `deploy.yml` pour générer ton site HTML avec :

- Date et heure du déploiement
    
- Commit Git
    
- Numéro du build GitHub Actions
    
- Image centrale
    
- Boutons « Rafraîchir » et « Voir le dépôt GitHub »
    
- Fichier `.nojekyll` pour GitHub Pages
    

> Assure-toi que le playbook crée bien le dossier `site/` avant de copier le fichier HTML.

* * *

## **4️⃣ Tester le playbook localement**

`ansible-playbook -i inventory.ini deploy.yml`

Vérifie que le dossier `site/` contient `index.html` et `.nojekyll`.

* * *

## **5️⃣ Créer le workflow GitHub Actions**

Crée `.github/workflows/deploy.yml` :

- Déclenché à chaque push sur `main`.
    
- Installe Python et Ansible.
    
- Exécute le playbook.
    
- Déploie le contenu de `site/` sur la branche `gh-pages`.
    

* * *

## **6️⃣ Configurer GitHub Pages**

- Dans **Settings → Pages** :
    
    - Source : branche `gh-pages`
        
    - Dossier : `/ (root)`
        
- Assure-toi que ton site HTML est à la racine (`index.html` dans `site/` sera copié à la racine de `gh-pages`).
    
- L’URL du site sera :
    

`https://<utilisateur>.github.io/ansible-gha-saas-demo/`

* * *

## **7️⃣ Déploiement automatique**

- Chaque push sur `main` déclenche le workflow GitHub Actions.
    
- Le contenu de `site/` est généré et poussé sur `gh-pages`.
    
- Tu peux vérifier le succès du workflow dans **Actions → dernier run**.
    

* * *

## **8️⃣ Fonctionnalités du site**

- Image centrale avec léger style CSS.
    
- Bouton **Rafraîchir la page**.
    
- Lien vers le **dépôt GitHub**.
    
- Footer fixe avec :
    
    - Date et heure du déploiement
        
    - Commit Git
        
    - Numéro du build GitHub Actions
        

* * *

## **9️⃣ Conseils et astuces**

- Pour tester le site localement, tu peux utiliser :

`python -m http.server 8080`

dans le dossier `site/` et éventuellement exposer via ngrok.

- Vérifie bien que `.nojekyll` est présent pour éviter que GitHub Pages ignore certains fichiers.
    
- Chaque push redéploie automatiquement le site.
