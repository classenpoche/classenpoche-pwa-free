# classenpoche-pwa-free
Ressources pédagogiques
# Classenpoche — PWA wrapper pour psteger.free.fr

Une app installable (Android, iOS, ordinateur) qui ouvre le site
`http://psteger.free.fr/` dans une coquille aux couleurs du site
(navy `#1e2e42` / accent ochre `#c98f3b`), avec icône, écran de démarrage,
et gestion propre du hors-ligne. Le contenu des cours reste chargé en
direct depuis le site — rien n'est dupliqué, donc les mises à jour du
site apparaissent immédiatement dans l'app.

## Fichiers

```
index.html          coquille de l'app (en-tête + iframe vers le site)
manifest.json        nom, icônes, couleurs, mode "standalone"
service-worker.js    met en cache la coquille seulement (pas les cours)
offline.html         page affichée si l'app elle-même est injoignable
icons/                icônes 192, 512 et 512 "maskable"
```

## ⚠️ Le point important à connaître : site en http, pas https

`psteger.free.fr` n'est servi qu'en **http**. Pour être installable, une
PWA doit elle-même tourner en **https**. Or un site https ne peut pas
toujours afficher en iframe un contenu http (blocage dit "contenu mixte"
par le navigateur) — le comportement diffère selon le navigateur et n'est
pas garanti dans le temps.

J'ai géré ça des deux côtés :

- **index.html** essaie de charger le site dans l'iframe. Si rien ne
  s'affiche au bout de 7 secondes (ou en cas d'erreur), l'app bascule
  automatiquement sur un écran "Ouvrir le site" qui l'ouvre en plein
  onglet du navigateur — l'app reste donc utile même si l'iframe est
  bloquée.
- Le **service worker** ne met en cache que la coquille (en-tête,
  icônes, page hors-ligne). Il n'essaie pas de mettre les cours en
  cache : comme ils viennent d'un domaine différent sans service worker,
  ce ne serait de toute façon pas fiable.

**Si tu as la main sur l'hébergement du site**, la vraie solution long
terme serait d'activer https sur psteger.free.fr (Free.fr le permet
via son interface d'hébergement) : l'iframe s'affichera alors sans
accroc, sans dépendre du mécanisme de secours.

## Déploiement

L'app doit être servie en **https** (obligatoire pour l'installation
et le service worker). Trois options simples, gratuites :

1. **GitHub Pages** : crée un dépôt, dépose ces fichiers à la racine,
   active Pages dans les réglages du dépôt.
2. **Netlify / Vercel** : glisser-déposer le dossier sur
   app.netlify.com/drop (aucun compte requis pour un essai rapide).
3. **Hébergement Free.fr existant**, si tu actives https dessus.

Aucune étape de build n'est nécessaire : ce sont des fichiers statiques.

## Tester en local

```bash
cd pwa
python3 -m http.server 8000
```

Puis ouvre `http://localhost:8000` — Chrome autorise l'installation en
PWA sur `localhost` même sans https, ce qui te permet de tester
l'installation et le service worker avant la mise en ligne.

## Personnalisation rapide

- Couleurs : variables CSS en haut de `index.html` (`--navy`, `--ochre`, `--parchment`).
- Icônes : regénérables facilement si tu veux repartir du vrai logo du
  site (`new-logo.jpg`) plutôt que du motif "chemin + repère" que j'ai
  dessiné en attendant.
- URL de démarrage : `HOME_URL` dans `index.html`.
