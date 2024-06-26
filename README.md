# Vue 3 + Vite

Pour rappel, voici la manière dont le code Javascript et ses composants Vue sont ajoutés et rendu disponible :

- le fichier index.html appel le fichier javascript main.js
- le fichier main.js importe le css et le composant principal depuis `./App.vue`
- le script Vue principal déclare le code qui sera injecté dans `#app` et fait l'import des composants Vue.

## Installation

```bash
$ yarn create vite  # répondre aux questions, notamment précisé le <projectName>
$ cd <projectName>
$ yarn              # yarn install
$ yarn dev          # lance le serveur de développement
```

## Suppression des fichiers d'exemple

Dans le fichier `App.vue`, supprimer le contenu des balises <script></script> et <style></style>.
Ne laisser que la balise <div></div> avec un <h1>ProjectName</h1> dans la balise <template></template>
Supprimer tout le contenu du fichier style.css
Supprimer dans le fichier index.html le contenu de la balise `<link />` et modifier le contenu de la balise <title></title>

Supprimer les fichiers suivants :

```bash
$ rm src/assets/* src/components/*
```

## Lancer l'application

Le CLI-Vite étant installé, l'application peut-être lancée comme ceci :

```bash
$ vite build  # construit l'app
$ vite serve  # lance le serveur Vite
```

## Ajout du logo Vue

Le svg a été téléchargé sur le net, puis déposé dans le répertoire `public`.
Pour l'affichage, il suffit d'utiliser la balise <img> :

```html
<img src="/vuejs-icon.svg" />  <!-- les fichiers statiques étant servis depuis la racine "public", inutile de préciser le chemin complet ../public/vuejs-icon.svg -->
```

## Ajout de l'icône Vue dans l'onglet du navigateur

Je réutilise le favicon disponible dans les projets Vue, sous public/favicon.ico.
Pour cela, je copie-colle le fichier dans le même répertoire `public`, puis j'ajoute ceci dans le <header> de `index.html`.

>NOTE : Comme indiqué précédemment, les trois écritures suivantes sont équivalentes :

```html
<link rel="icon" href="./public/favicon.ico">
<link rel="icon" href="/public/favicon.ico">
<link rel="icon" href="/favicon.ico">
```

## Utilisation de l'id comme clé

Plutôt que d'utiliser l'index comme clé dans la boucle for, je me sers de l'id disponible dans task.
Les modifications sont apportées ligne 9 du fichier `App.vue` avec `:key="item.id"`.

## Troubleshooting

Problème rencontré lors de l'ajout de la directive `@submit.prevent="onSubmit"` : celle-ci étant positionnée sur un élément `<button>`, elle ne peut fonctionner puisque spécifique à l'élément `<form>`.
Après correction de `@submit` pour `@click`, le problème a été résolu.

Un autre problème rencontré a été celui du footer et de son positionnement `sticky`. Pour que ce dernier fonctionne, il faut prendre en compte son `container`, c'est-à-dire l'élément qui le contient. Or, si l'on veut intégrer ce footer dans un composant (comme `App.vue` par exemple), il faut tenir compte des balises `<template></template>` qui encadrent nécessairement ce dernier. Bien qu'elles ne soient pas rendues, ces balises interfèrent avec l'élément : le footer est alors sticky relativement à `<template>` et non plus au `<body></body>`.
Après avoir replacé le `<footer>` dans le fichier `index.html`, tout est rentré dans l'ordre.

J'ai rencontré un problème avec un _event listener_ que j'ai déclaré dans mon code.
Celui-ci était signalé comme étant non déclaré par les dev tools.
Après quelques recherches, j'ai compris que depuis Vue3, que ce soit avec l'api option ou composition, il est nécessaire de déclarer les _event listeners_ utilisés. Il s'agit d'un mode plus strict d'utilisation.
Pour déclarer celui-ci, il suffit d'ajouter la clé `emits` au composant qui le déclare, puis de renseigner en valeur un tableau contenant l'_event listener_ :

```js
emits: ['todo-added']
```

Lors du test de l'appli hors connexion, les fonts ne sont pas disponibles en raison d'un chargement par CDN.
Il faut donc étudier la possibilité de récupérer les fonts sur le poste, pour les rendre ensuite disponibles sur le poste de l'utilisateur.

Pour interpréter le symbole `x` utilisé pour signifier la suppression d'une tâche, il faut utiliser la séquence d'échappement Unicode `\u`, ce qui donne `{{ '\u00D7' }}`.

J'ai rencontré un problème lors de l'utilisation d'un eventListener nommé `delete`. Il s'avère qu'il faut veiller à ne pas utiliser des noms réservés propres au langage Javascript. En effet, ici, `delete` est un nom onBuilt de JS.
Pour régler le problème, il suffit de renommer la méthode, par exemple sous le nom `handleDelete`.

J'ai ajouté un eventListener pour vider le champ input du formulaire lorsque la touche `ENTREE` est pressée, mais sans succès.
Le problème venait de l'utilisation du modificateur `lazy` sur le `v-model` qui semble entrer en conflit avec la directive précédente.
Le modificateur .lazy pour v-model retarde la mise à jour de la valeur liée jusqu'à ce que l'élément perde le focus (c'est-à-dire que l'utilisateur quitte le champ de saisie). Cela signifie que si l'on utilise le modificateur `.lazy` sur `v-model`, les mises à jour de la propriété `this.label` ne se produiront qu'après que l'utilisateur aura quitté le champ de saisie (par exemple, en cliquant en dehors du champ de saisie ou en appuyant sur la touche "Tab").
Après avoir supprimé le modificateur, le champ du formulaire est bien vidé lorsque la touche `ENTREE` est pressée.

Du fait qu'il y a plusieurs eventListener sur un même composant et qu'il existe en outre des composants inclus dans d'autres, lorsqu'un évènement est émis ce sont tous les évènements autour qui sont déclenchés. Il faut trouver à isoler la propagation des évènements.

J'ai eu des difficultés à gérer les eventListeners multiples sur un même composant, notamment concernant ceux que j'ai voulu implémenter pour l'édition d'une tâche.
Afin de surmonter cette difficulté, j'ai opté pour une réécriture du composant en deux éléments distincts : ToDoItemDisplay et ToDoItemEdit.
Ceci m'a permis de contourner les interférences entre les event Listeners posés sur un même élément.

Pour gérer l'affichage du label de la tâche lors de son édition, j'ai dû ajouter une propriété dans data() pour être en mesure de changer l'état de la props transmise (label).
J'ai également dû créer un eventListener pour émettre le statut de la tâche isEditing=false lors du clic sur l'icône d'abandon de la modification.

Pour ajouter le focus sur la tâche courante au clic sur l'icône d'édition, j'ai utilisé la propriété `ref` de Vue pour accéder aux éléments du VirtualDOM et les manipuler.

Après voir déclarer ma ref sur l'input, j'ai déclaré le focus sur cet élément lors d'une des étapes du cycle de vie des composants Vue : `mounted()`. (Doc [Mozilla](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_refs_focus_management))

## Configuration de l'application pour l'intégrer aux Github pages

Afin d'ajouter mon application Vue à mon portfolio hébergé sur github-pages, il m'a fallu configurer l'appli.

Pour cela, j'ai d'abord créé un fichier `vue.config.js` avec le contenu suivant :

```js
module.exports = {
    publicPath: process.env.NODE_ENV === 'production' ? 'https://github.com/mickael-Bula/todo-List-Vue' : '/'
}
```

J'ai également déclaré la racine de l'appli à déployer sur github dans le fichier `vite.config.js` :

```js
export default defineConfig({
  plugins: [vue()],
  base: '/todo-List-Vue/',
})
```

J'ai ensuite installé un package permettant de gérer l'exposition du build de l'application sur github-pages :

```bash
$ npm install gh-pages
```

J'ai ensuite mis à jour le fichier `package.json` en ajoutant une commande dans `script`: 

```json
{
  "deploy": "npm run build && gh-pages -d dist"
}
```

L'ajout de cette commande me permet de lancer le build de l'application dans un répertoire `dist` dédié :

```bash
$ npm run deploy
```

J'ai demandé à `git` de créer une branche distante pour accueillir le build que je commite avant de le pousser :

```bash
$ git add dist
$ git commit -m "Ajout du build de l'application"
$ git subtree split --prefix dist -b gh-pages
$ git push origin gh-pages
```

Il faut ensuite suivre les instructions disponibles sur le site de [Vite](https://vitejs.dev/guide/static-deploy.html)
et y récupérer le workflow de déploiement.

Avec cette configuration, le build de l'application sera lancé automatiquement à chaque nouveau push sur la branche principale.

Ne reste plus qu'à visiter la page `https://mickael-Bula.github.io/todo-List-Vue/` pour en voir le résultat.

## TODO

- Vérifier la taille du logo Vue selon les écrans. Adapter sa hauteur en conséquence (trop grand actuellement).
- Voir si les couleurs (rouge notamment) se fondent bien dans la nouvelle charte graphique verte de Vue.
- Ajouter un personnage svg dans le portfolio après avoir suivi tuto pour découper du contenu dans une image téléchargée (C:\Users\bulam\Documents\images SVG)
- utiliser la méthode d'édition déjà déclarée dans le composant principal ou modifier les boutons lors de l'édition
- Ajouter les icônes de fontawesome en tant que composants (https://fontawesome.com/docs/web/use-with/vue/)
- Ajouter des transitions pour les styles au survol des éléments (tasks, icônes...)
- Ajouter la possibilité de remonter ou descendre une tâche dans la liste pour ordonner l'ordre de priorité (raccourci clavier CTRL + &uarr; et &darr; ou [drag&drop](https://learnvue.co/articles/vue-drag-and-drop))
- Ajouter un bouton Annuler après Valider pour supprimer une tâche saisie par erreur