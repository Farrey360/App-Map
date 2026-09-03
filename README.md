# BoP Map Editor 2

**Éditeur de carte pour peindre des provinces à la main**, pensé pour une tablette au
stylet (marche aussi au PC à la souris). On peint chaque province comme un **aplat de
couleur**, on les regroupe en **pays** puis en **régions**, on renseigne quelques
infos (terrain, code pays, population), et on **exporte** une carte d'ids + trois
fichiers JSON prêts pour un moteur de jeu.

C'est une **application web autonome** : un seul fichier `index.html`, aucune
dépendance, aucune installation, aucun compte. Tout est stocké dans le navigateur ;
rien n'est envoyé sur un serveur.

## Essayer

👉 **<https://farrey360.github.io/App-Map/>**

Ouvre le lien, crée un projet, dessine. Sur mobile/tablette tu peux l'**installer**
(menu du navigateur → « Installer l'application ») : elle s'ouvre alors en plein
écran et fonctionne **hors-ligne**.

## Principe

- On dessine **par-dessus le vide uniquement** : impossible d'écraser une province
  voisine par accident. Les provinces se touchent bord à bord, sans contour ni trou.
- Fermer le tour d'une province **remplit l'intérieur automatiquement** au lever du
  stylet (les très grandes formes : utiliser le Pot).
- Hiérarchie **Pays → Régions → Provinces**. Une province appartient à une région,
  une région à un pays.

## Les modes

En haut de la barre de gauche, **un bouton bascule** entre **Peinture** et
**Édition**. En Peinture, trois petits boutons choisissent la couche :
**Provinces** · **Pays** · **Régions**. La touche `M` fait défiler les quatre.

### Peinture › Provinces

| | |
|---|---|
| **+** / `N` | nouvelle province (couleur unique auto ; recliquer sans dessiner ne recrée rien) |
| Pinceau `B` | peint la province active — ne recouvre que le vide |
| Gomme `E` | repasse une zone en vide |
| Pot `G` | remplit d'un coup une zone vide fermée |
| Pipette `I` | reprend une province existante (pour l'agrandir) |
| `[` `]` | taille du pinceau |

### Peinture › Pays

Chaque province se colore selon son pays ; celles sans pays restent sombres.

| | |
|---|---|
| **+** / `N` | nouveau pays |
| Assigner `A` | clic / glissé sur des provinces → elles rejoignent le pays actif |
| Pipette `I` | rend actif le pays sous le curseur |
| case « Retirer » ou clic droit | enlève la province de son pays |
| liste déroulante / champ nom | pays actif et son nom |

### Peinture › Régions

Fonctionne comme le mode Pays. On crée une région, on peint des provinces dessus ;
le **pays** de la région est **déduit automatiquement** des provinces peintes — rien
à choisir.

| | |
|---|---|
| **+** / `N` | nouvelle région |
| Assigner `A` | clic / glissé sur des provinces → région active |
| Fusionner `R` | touche une province → sa région est absorbée par la région active |
| Pipette `I` | rend active la région sous le curseur |
| case « Retirer » ou clic droit | enlève la province de sa région |

### Terre ou mer

Dans la barre du haut en mode **Province**, le bouton **Terre / Mer** (`T`) décide de ce
que seront les **prochaines** provinces créées. Une province maritime reçoit d'office le
terrain « mer / océan » et une teinte **bleu sombre**, pour la reconnaître d'un coup d'œil
en peignant. Changer le terrain d'une province vers (ou depuis) l'eau met sa teinte à jour
toute seule — le type suit toujours le terrain, il n'y a pas deux réglages à tenir d'accord.

### Édition

Bouton bascule en haut (icône presse-papier). Trois cibles — **Province** `1` /
**Pays** `2` / **Région** `3`.

| Cible | Comment |
|---|---|
| **Province** | **stylo terrain** : choisir un terrain dans la palette du bas (`[` `]` pour changer), puis glisser sur la carte — chaque province touchée prend ce terrain. Clic droit = « non défini ». |
| **Pays** | clic sur un pays → panneau : nom + code 3 lettres (généré du nom, ex. *France → FRA*, modifiable ; vider = revenir à l'auto). |
| **Région** | clic sur une région → panneau : nom, nombre d'habitants. |

`Ctrl+Z` / `Ctrl+Y` = annuler / rétablir (le dessin, les assignations et le terrain ;
pas les noms). `F` = voir toute la carte. `H` = main.

**Gestes tablette :** deux doigts = déplacer + pincer pour zoomer. Quand le stylet
touche, doigt et paume sont ignorés.

## Fonds de référence (calques)

Bouton image de la barre de gauche, ou « Fonds » dans Réglages. Jusqu'à **3 images**,
chacune un calque indépendant : visibilité, opacité, ordre (glisser la poignée),
renommer, supprimer, et **déplacer / redimensionner** par poignées sur la carte
(le ratio de l'image est conservé). Les PNG transparents sont respectés. Chaque
calque garde sa position et sa taille propres : agrandir la zone de travail ne
déplace ni n'étire un fond déjà en place.

## Projets et sauvegarde

- **Écran d'accueil** : la liste des projets (vignette, nom, date, taille), une carte
  **Nouveau** et une carte **Importer .bopmap**. Chaque carte a un **×** pour
  supprimer le projet.
- **Sauvegarde auto** dans le navigateur (IndexedDB) quelques secondes après chaque
  modification. Bouton **« Sauvegarder maintenant »** dans Réglages pour forcer
  l'écriture avant de fermer.
- **Sauver le projet** → fichier **`.bopmap`** autonome (dessin + pays + régions +
  terrains + infos + **les images de fond**). À déplacer entre appareils ;
  « Importer .bopmap » l'ajoute comme **nouveau** projet (n'écrase jamais celui
  ouvert). Les anciens `.bopmap` (v1–v4) s'ouvrent aussi, sans fond.
- **Résolution libre par projet** (défaut 10976 × 4096, jusqu'à 40 000 px de côté /
  200 Mpx). Agrandissable ensuite (Réglages → Zone de travail), jamais réductible.

## Export

Réglages → **Export complet**. Télécharge dans un dossier daté (ex.
`bme2_export_2026-09-03/`) :

- **`provinces.png`** — couleur = id de province, à la résolution du projet.
- **`provinces.json`** — `{ format:2, w, h, unpainted:[0,0,0], terrains, provinces:[{id, color, country, region, terrain, type}] }`
  (seulement les provinces qui ont encore des pixels). `type` vaut `land`, `sea` ou
  `lake`, déduit du terrain : c'est ce que le moteur lit pour savoir ce qui est de l'eau.
  `unpainted` est la couleur des pixels sans province dans le PNG — à traiter comme du vide.
- **`regions.json`** — `{ regions:[{id, name, country, population, provinces:[ids]}] }`.
- **`countries.json`** — `{ countries:[{id, name, tag, regions:[ids]}] }`.

## Faire tourner en local

Aucun build. Il suffit d'un serveur de fichiers statique dans le dossier du projet :

```sh
python -m http.server 8777
# puis http://localhost:8777
```

Ou n'importe quel équivalent (`npx serve`, extension VS Code Live Server, etc.).
Ouvrir `index.html` en `file://` marche pour dépanner, mais sans installation PWA
ni service worker.

Pour une **vraie installation plein écran** sur tablette, l'app doit être servie en
**HTTPS** (ou `localhost`) — c'est le rôle du déploiement GitHub Pages ci-dessus. En
`http://` sur une IP de réseau local, Chrome ne propose qu'un raccourci qui rouvre
le navigateur ; le bouton **« Plein écran »** de Réglages dépanne dans ce cas.

## Détails techniques

- `index.html` seul, JavaScript « vanilla ». `sw.js` : service worker (cache hors-ligne
  quand servi en http/https). `manifest.webmanifest` : PWA `display: fullscreen`.
- `ids` = `Uint16Array` de W × H (une province par pixel, 0 = vide) ; W/H variables
  par projet.
- `provCountry` / `provRegion` = `Uint16Array` indexés par id de province.
  `provTerrain` = `Uint8Array`. `regions[rid]` = `{name, country, pop}`,
  `countries[cid]` = `{name, tag}`.
- Rendu : rastérisation CPU du buffer vers un `<canvas>` à la taille de l'écran
  (pas de canvas géant, pas de limite mobile) ; seule la zone modifiée est
  recalculée pendant un tracé.
- Calques de fond : jusqu'à 3, position/taille en coordonnées monde, composés par
  opacité et alpha par pixel.
- IndexedDB : store `projects` (un enregistrement par projet, images comprises).

`_godot_jalonA/` (non versionné) : une première tentative en Godot, abandonnée.

## Licence

[PolyForm Noncommercial 1.0.0](LICENSE) — © 2026 Farrey.

Libre d'utilisation, de modification et de partage **pour tout usage non
commercial** (perso, associatif, éducatif, recherche…). L'usage commercial
(revente, intégration dans un produit ou un service payant) nécessite un accord
séparé — ouvrez une *issue* pour en discuter.

## Contribuer

Bugs et idées : ouvrez une *issue*. L'app tient dans un fichier, les *pull requests*
sont les bienvenues.
