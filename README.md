# Vue 3 + Vite

## Installation

```bash
$ yarn create vite  # répondre aux questions, notamment précisé le <projectName>
$ cd <projectName>
$ yarn              # yarn install
$ yarn dev          # lance le serveur de développement
```

## Suppression des fichiers d'exemple

Dans le fichier App.vue, supprimer le contenu des balises <script></script> et <style></style>.
Ne laisser que la balise <div></div> avec un <h1>ProjectName</h1> dans la balise <template></template>
Supprimer tout le contenu du fichier style.css
Supprimer dans le fichier index.html le contenu de la balise <link /> et modifier le contenu de la balise <title></title>

Supprimer les fichiers suivants :

```bash
$ rm src/assets/* src/components/*
```

## TODO

- Vérifier la taille du logo Vue selon les écrans. Adapter sa hauteur en conséquence (trop grand actuellement).
- Voir si les couleurs (rouge notamment) se fondent bien dans la nouvelle charte graphique verte de Vue.
- Ajouter un personnage svg dans le portfolio après avoir suivi tuto pour découper du contenu dans une image téléchargée (C:\Users\bulam\Documents\images SVG)
- utiliser les méthodes de suppression et d'édition déjà déclarée dans le composant principal
- Réutiliser les styles pour les boutons d'édition plutôt que les cases à cocher

## Ajout du logo Vue

Le svg a été téléchargé sur le net, puis déposé dans le répertoire `public`.
Pour l'affichage, il suffit d'utiliser la balise <img> :

```html
<img src="/vuejs-icon.svg" />  <!-- les fichiers statiques étant servis depuis la racine public, inutile de préciser le chemin complet ../public/vuejs-icon.svg -->
```

## Ajout de l'icone Vue dans l'onglet du navigateur

Je réutilise le favicon disponible dans les projets Vue, sous public/favicon.ico.
Pour cela, je copie-colle le fichier dans le même répertoire `public`, puis j'ajoute ceci dans le <header> de `index.html` :

```html
<link rel="icon" href="./public/favicon.ico">
```

## Utilisation de l'id comme clé

Plutôt que d'utiliser l'index comme clé dans la boucle for, je me sert de l'id disponible dans task.
Les modifications sont apportées ligne 9 du fichier App.vue avec `:key="item.id`.

## Troubleshootings

Problème rencontré lors de l'ajout de la directive `@submit.prevent="onSubmit"` : celle-ci étant positionnée sur un élément `<button>`, elle ne peut fonctionner puisque spécifique à l'élément `<form>`.
Après correction de `@submit` pour `@click`, le problème a été résolu.

Un autre problème rencontré a été celui du footer et de son positionnement `sticky`. Pour que ce dernier fonctionne, il faut prendre en compte son `container`, c'est-à-dire l'élément qui le contient. Or, si l'on veut intégrer ce footer dans un composant (comme App.vue par exemple), il faut tenir compte des balises `<template></template>` qui encadre nécessairement ce dernier. Bien qu'elles ne soient pas rendues, ces balises interfèrent avec l'élément : le footer est alors sticky relativement à `<template>` et non plus au `<body></body>`.
Après avoir replacé le `<footer>` dans le fichier `index.html`, tout est rentré dans l'ordre.

J'ai rencontré un problème avec un event-listener que j'ai déclaré dans mon code.
Celui-ci était signalé comme étant non déclaré par les dev tools.
Après quelques recherches, j'ai compris que depuis Vue3, que ce soit avec l'api option ou composition, il est nécessaire de déclarer les event-listener utilisés. Il s'agit d'un mode plus strict d'utilisation.
Pour déclarer celui-ci, il suffit d'ajouter la clé `emits` au composant qui le déclare, puis de renseigner en valeur un tableau contenant l'event-listener :

```js
emits: ['todo-added']
```

Lors du tesst de l'appli hors connexion, les fonts ne sont pas disponibles en raison d'un chargement par CDN.
Il faut donc &tudier la possibilité de récupérer les fonts sur le poste, de les rendre disponibles sur le poste de l'utilisateur.

Pour interpréter le symbole `x` utilisé pour signifier la suppression d'une tâche, il faut utiliser la s"quence d'échappement Unicode `\u`, ce qui donne `{{ '\u00D7' }}`.

J'ai rencontré un problème lors de l'utilisation d'un eventListener nommé `delete`. Il s'avère qu'il faut veiller à ne pas utiliser des noms réservés propres au langage Javascript. En effet, ici, `delete` est un nom onBuilt de JS.
Pour régler le problème, il suffit de renommer la méthode, pour exemple sou le nom `handleDelete`.