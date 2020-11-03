# Workflows courants

Imaginons...

Je développe la fonctionnalité f5

Un nouveau composant doit être créé <SomeComponent />

Je crée donc une branche dans laquelle j'aurais 2 commits :

```bash
bksfh: feat(feature/f5): Creation de la fonctionnalité f5
aaaaa: feat(design/system): create <SomeComponent/>
```

Afin de faciliter la review du composant, je vais le sortir dans une branche séparée avec le seul commit du composant extrait à l'aide d'un git cherry-pick :

```bash
aaaaa: feat(design/system): create <SomeComponent/>
```

Cette nouvelle branche, tirée de `develop`, est l'objet d'une PR à destination de `develop`.

Imaginons qu'a la review nous aillons les deux retours suivants :

- Rajoute ca (\*1)
- Renomme ça (\*2)

Alors, on va rajouter ces deux commits :

```bash
bbbbb: fix(design/System): fix *1
ccccc: fix(design/System): fix *2
```

Vous aurez sûrement une nouvelle review.  
Le fait d'avoir découpé les fix en commits séparés vont faciliter la relecture du relecteur.

Une fois la review terminée, ce serait un peu moche de laisser les deux commits de fix dans l'historique.  
On va donc la réécrire (l'historique) afin de fusionner les commits de correction avec le commit initial :

`git rebase -i HEAD~3`

On va alors se retrouver dans une éditeur avec le contenu suivant :

```bash
  pick aaaaa: feat(design/system): create <SomeComponent/>
  pick bbbbb: fix(design/System): fix *1
  pick bbbbb: fix(design/System): fix *2
```

On va remplacer les `pick` des deux fixes par `fixup`.

```bash
  pick aaaaa: feat(design/system): create <SomeComponent/>
  fixup bbbbb: fix(design/System): fix *1
  fixup ccccc: fix(design/System): fix *2
```

Ici, on ordonne à git de fusionner les commits `bbbbb` et `ccccc` dans le commit `aaaaa`.  
Nous allons donc nous retrouver avec l'historique suivant :

```bash
  pick abcabc: feat(design/system): create <SomeComponent/>
```

Et 💥 notre PR du design system est prête à être mergé 💪

> downside possible : ré-approbation / à vérifier, le hash devrait être le même

Bon, reprenons notre PR de feature.  
On veut récupérer le composant finalisé et merger sur develop.
On va donc se mettre sur notre branche de feature, et rebase la `develop`.

On va se retrouver avec un conflit de manière certaine.  
Lors du conflit, on va choisir les modifications venant de la `develop`.  
Le commit initial sur la branche de feature **sera donc vide**.  
Le **working directory** et les **staged files**, on va donc utiliser la commande `git rebase --skip` pour dire a git "t'inquiètes ce commit ne nous intéressait pas" et il va donc continuer et finir son rebase.

On se retrouve donc avec :

- notre branche de feature qui ne contient **que** notre commit de feature,
- le commit design system qu'on avait commit sur notre branche aura **disparue** 👻 🔥