# Lab Ansible pour la classe : configurer une machine avec un playbook

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml)

Un **lab Ansible** prêt à l’emploi pour enseigner l’infrastructure as code, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les établissements scolaires donnent à chaque élève ses propres machines et son terminal dans le navigateur. Chaque élève reçoit une VM Ubuntu avec **Ansible** installé et un projet contenant un **playbook** inachevé. Il le termine pour **installer un paquet**, **créer un fichier à partir d’un modèle Jinja2 avec une variable** et **écrire un fichier d’unité systemd**, puis le relance et constate `changed=0` : c’est l’**idempotence**, l’idée qui rend Ansible utile à apprendre. Rien n’est installé sur l’ordinateur de l’élève.

## Ce que fait l’élève

Le lab laisse un projet dans `~/ansible-lab` (un `inventory.ini` pour cette seule machine en connexion locale, un `ansible.cfg`, un modèle et un `site.yml` à moitié écrit avec trois TODO) et un `README.md` dans le dossier personnel qui explique quoi faire sans donner aucune réponse :

1. Installer un paquet avec un module de gestion de paquets.
2. Créer `/etc/ansible-lab/welcome.txt` à partir du modèle, appartenant à root, avec les droits 0644.
3. Écrire un fichier d’unité systemd dont la description contient la variable du nom du site.
4. Relancer le playbook : une seconde exécution doit afficher `changed=0`.

## Comment il est noté

Le lab comporte une **vérification**, un script qui note une VM rendue sans que l’enseignant ait à la lire. Elle s’exécute sur une copie de la machine enregistrée, après un démarrage à neuf. Elle lit le YAML et refuse un playbook fait de tâches `shell`, `command`, `raw` ou `script`, d’inclusions et de rôles. Elle lit le paquet et les fichiers enregistrés, puis **exécute deux fois le playbook de l’élève avec un nom de site que personne n’a jamais vu** : ce que le playbook produit doit le contenir, ce qu’un fichier écrit à la main ne fait pas, et la seconde exécution doit afficher `changed=0`, ce qu’une tâche qui modifie quelque chose à chaque fois ne fait pas. La première ligne affichée dit ce qui ne va pas.

Ce qu’elle ne fait pas : elle lit le fichier d’unité sans jamais démarrer le service, et un élève qui a les droits root sur sa propre VM pourrait encore trafiquer Ansible lui-même. C’est une vérification pour la classe, pas une preuve.

## Ce que contient le fichier

`ansible-basics.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe `ansible-core` depuis l’archive d’Ubuntu (une dizaine de secondes) et écrit le projet et le README |
| `check` | le script de notation, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple Ansible, ou collez le contenu de `ansible-basics.lab.yaml`. Utiliser un exemple remplace le texte, et Annuler rétablit le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève a sa propre VM, conservée d’une session à l’autre.
4. Distribuez-le comme n’importe quelle tâche. Une fois rendu, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et indique si elle a réussi et ce qu’elle a affiché.

La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-ansible-basics) dans le manuel, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des exemples de labs](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste.

Pas encore de compte pour votre établissement ? [Votre première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [l’inscription](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérifier le fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est un YAML valide avec les cinq clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne fonctionne pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre. C’est pourquoi ce lab est **uniquement Ansible** : Terraform télécharge ses fournisseurs depuis un registre que la VM ne peut pas joindre, il n’est donc pas proposé. La cible est la VM elle-même, en connexion locale, puisqu’il s’agit d’une seule machine.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) et [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

[Ansible](https://www.ansible.com) (`ansible-core`, GPL-3.0) est installé depuis l’archive d’Ubuntu au démarrage d’une VM ; rien n’en est distribué ici.
