# Autotour : visite 360° de véhicules, simple et à petit prix

**Nom de travail : `autotour`** (dépôt GitHub, projet Vercel). Le nom définitif reste ouvert, voir la section 10.

> Document de travail vivant. Tout ce qui est écrit ici est une **piste ou une hypothèse**, pas une décision. Chaque section peut être remise en question dès qu'une meilleure idée apparaît.

---

## 1. Vision

Permettre à **n'importe qui** (particulier, petit garage, concession) de créer en quelques minutes une visite d'un véhicule, extérieur et intérieur, avec :

- une capture **simple** (une vidéo, ou des photos si l'utilisateur préfère) ;
- un **paiement léger** (de l'ordre de 2 à 3 € par véhicule, à valider) ;
- un **lien unique** utilisable partout : annonces, sites de garages, messages, plateformes.

L'idée est de ne pas avoir à intervenir après la mise en service : l'utilisateur doit pouvoir y arriver seul.

---

## 2. Ce que nous avons compris de la concurrence

### Le fonctionnement commun

Presque tous les acteurs suivent la même chaîne :

1. **Capture guidée** par une application mobile (repères à l'écran, photos successives).
2. **Traitement automatique dans le cloud** (détourage du fond, correction des reflets, floutage de la plaque, assemblage du spin).
3. **Diffusion** par lien ou code d'intégration, parfois connectée aux logiciels de gestion des concessions.
4. **Abonnement** (mensuel ou au véhicule).

### Les acteurs repérés

| Acteur | Ce qu'on en retient |
|---|---|
| **Spyne** | App de capture guidée + IA (fond, ombres, reflets, plaque). Forfaits annoncés à partir de 350 $/mois. |
| **CarCutter by Meero** (France) | Détourage IA intégré dans une app smartphone. Cible : distributeurs, constructeurs, loueurs. |
| **Glo3D, Via360** | Spin 360° au smartphone, tour en quelques minutes. |
| **SpinCar / VC360** | Solutions plus lourdes (matériel dédié). Prix français de 2016 : environ 599 € + 250 €/mois (à revérifier). |
| **Studios industriels** (ex. groupe Dubreuil) | Plus de 200 000 € investis, environ 12-13 min par voiture. Réservé aux gros groupes. |
| **Photographes indépendants** | Visites 360° à la prestation (Ricoh Theta, Matterport). Manuel, peu scalable. |

> Les prix ci-dessus viennent de sites publics ou d'articles parfois anciens. Ils sont à revérifier avant toute décision.

### Ce qui semble laissé ouvert

- **Le prix** : 350 $/mois est élevé pour un petit garage ou un particulier.
- **Les particuliers** : peu ciblés par les acteurs pro.
- **L'intérieur** : souvent secondaire dans les offres.
- **Le marché français** à petit prix, à explorer.

---

## 3. Comprendre le rendu « 3D »

Deux mécanismes très différents se cachent derrière ce mot.

### 3.1 Le spin 360° (images qui défilent)

- On prend 24 à 36 images en tournant autour du véhicule (issues de photos ou extraites d'une vidéo).
- Le visualiseur passe d'une image à la suivante selon le glissement du doigt, comme un flipbook.
- **Aucun calcul 3D** : rapide, fiable, très peu coûteux.

### 3.2 La vraie 3D (photogrammétrie, NeRF, Gaussian Splatting)

1. Repérer les mêmes points dans plusieurs images.
2. Calculer la position de la caméra pour chaque image.
3. Reconstruire la forme (maillage texturé) ou la scène (millions de « taches » de couleur).
4. Afficher avec déplacement libre.

**Difficulté sur un véhicule** : reflets et vitres changent selon l'angle, ce qui perturbe le repérage des points. Résultat possible : carrosserie déformée ou bruitée. Les véhicules mats ou les showrooms bien éclairés donnent de meilleurs résultats.

**Coût** : le calcul se fait sur des cartes graphiques (GPU), ce qui coûte à chaque véhicule traité. À mesurer pour voir si ça reste compatible avec un prix de 2-3 €.

### 3.3 L'intérieur

Une **photo 360° sphérique** prise depuis un point fixe : on regarde autour de soi, sans se déplacer. C'est de l'image, pas de la 3D. Des points cliquables (compteur, écran, sièges, coffre) peuvent l'enrichir.

---

## 4. Pistes de produit

Ces pistes peuvent être combinées, testées puis abandonnées.

### Piste A : le spin simple (base)
- Extérieur : spin à partir d'une vidéo ou de photos.
- Intérieur : photo 360°.
- Points forts : fiable, très bon marché.
- Point d'attention : « 36 photos » n'est pas un argument de vente. Ce qui compte, c'est le résultat pour l'acheteur.

### Piste B : la visite « transparence » (à explorer en priorité)
- Points cliquables sur les **défauts** (rayure, jante abîmée) et sur les **équipements**.
- Horodatage possible, pour montrer que les images sont récentes.
- Ce que les gens achètent ici : **la confiance**, notamment face aux arnaques sur les annonces entre particuliers.

### Piste C : la vidéo interactive
- L'acheteur glisse le doigt pour avancer ou reculer dans la vidéo du tour du véhicule.
- L'utilisateur perçoit une vidéo fluide, la technique dessous reste simple.

### Piste D : la vraie 3D (option « wow »)
- Reconstruction à partir d'une vidéo avec un outil existant (Luma, Polycam, Scaniverse, Kiri Engine, ou Nerfstudio en open source).
- À tester sur 2 ou 3 véhicules avant de décider : qualité des reflets, temps de calcul, coût par véhicule.

### Idées complémentaires à garder sous la main
- Détourage du fond en option payante (le fond d'origine est conservé au départ).
- Floutage automatique de la plaque.
- Rapport d'état du véhicule joint à la visite.
- Visite partageable par QR code (pratique en garage ou sur le pare-brise).

---

## 5. Construire ou assembler ?

Piste retenue pour l'instant : **assembler des briques existantes et ne fabriquer que ce qui fait la différence**.

### Briques existantes à explorer

| Besoin | Options possibles |
|---|---|
| Visionneuse de spin | Bibliothèques open source de 360° par images |
| Visionneuse photo sphérique | Bibliothèques open source (ex. Photo Sphere Viewer), outils comme Kuula ou Pano2VR pour prototyper |
| Paiement | Stripe |
| Comptes et stockage | Services cloud clé en main |
| Floutage de plaque | Modèles open source ou API (à comparer sur le coût) |
| Vraie 3D (option) | Luma, Polycam, Scaniverse, Kiri Engine, Nerfstudio |

### À fabriquer
- La **page de capture** (filmer, envoyer, guider pour un tour régulier).
- Le **traitement** : extraire les bonnes images d'une vidéo, assembler le spin.
- Le **lien de partage** et l'intégration dans les annonces.

### Pourquoi ne pas revendre un outil existant
- Le coût d'un outil pro (plusieurs centaines de dollars par mois) empêche de descendre à 2-3 € par véhicule.
- Peu de contrôle sur le produit et dépendance à un tiers.

---

## 6. Architecture possible (v1)

```
Utilisateur (téléphone)
   │  filme le tour du véhicule + photo 360° intérieure
   ▼
Page web d'envoi
   ▼
Traitement (cloud)
   ├─ extraction des images nettes de la vidéo
   ├─ (option) floutage de la plaque
   └─ assemblage du spin
   ▼
Visionneuse + lien unique
   ├─ lien à coller dans une annonce
   ├─ code d'intégration (iframe) pour un site
   └─ (plus tard) API pour les plateformes
```

Une application mobile n'est probablement pas nécessaire au départ : une page web ouverte sur le téléphone peut suffire.

---

## 6 bis. Prototypes existants et stack envisagée

### Prototypes actuels (HTML autonomes)

| Fichier | Rôle | État |
|---|---|---|
| `visite-360.html` | Visualiseur de spin : véhicule fictif de démonstration, import d'une vidéo (36 images extraites) ou de photos | Fonctionne. La démo est volontairement basique, le vrai test se fait avec une vidéo réelle. |
| `capture-guidee.html` | Capture guidée : caméra arrière (haute résolution demandée), cadre, ligne d'horizon, tour mesuré avec les capteurs, **image la plus nette gardée pour chaque tranche de 10°** (contre le flou de mouvement), rappel de passer en paysage, guidage par **flèches qui clignotent** (sens de marche, ralentir, redresser, relever ou baisser le téléphone), cadre vert / orange / rouge, vibrations, pourcentage du tour. Résultat à faire tourner au doigt, avec **enregistrement de la visite en fichier HTML autonome** (partageable, sans serveur) | Testé une première fois sur un vrai téléphone : le tour, les images et le rendu fonctionnent. Points constatés : flou de mouvement, véhicule petit dans l'image, format portrait. |

Ces fichiers servent à valider le rendu et la capture. Ils peuvent rester en HTML tant que le concept n'est pas validé, puis être repris dans le futur projet.

**Constat important** : depuis l'application Claude, la caméra est bloquée pour les pages publiées. La caméra demande une adresse en **https** ouverte dans un navigateur. Un hébergement à soi (Vercel ou GitHub Pages) règle ce point.

### Stack envisagée pour la suite

| Besoin | Piste |
|---|---|
| Site et espace revendeur | React avec Next.js |
| Hébergement (https inclus) | Vercel, depuis un dépôt GitHub (privé possible) |
| Comptes, base de données, stockage des images | Supabase |
| Paiement | Stripe |
| Visualiseur et capture | Code JavaScript léger, repris des prototypes, pour pouvoir l'intégrer ailleurs (sites de garages, annonces) |

**Simplification** : l'extraction des images se fait dans le téléphone. Il n'y a donc pas de serveur de traitement vidéo au départ, seulement l'envoi de 36 images. Cela aide à garder le coût par véhicule très bas.

### Structure de dépôt possible

```
autotour/
├─ README.md
├─ prototypes/
│   ├─ capture-guidee.html
│   └─ visite-360.html
└─ app/            (projet Next.js, à créer une fois le rendu validé)
```

### Mise en ligne des prototypes

1. Créer le dépôt GitHub (`autotour`).
2. Y déposer les fichiers, en commençant par `capture-guidee.html`.
3. Connecter le dépôt à Vercel pour obtenir une adresse https.
4. Ouvrir cette adresse sur téléphone et tester sur un vrai véhicule.

---

## 7. Modèle économique (hypothèses)

- **Gratuit** : visite basique avec le logo du service (chaque annonce fait de la publicité).
- **Environ 2-3 €** : sans logo, points sur défauts, lien permanent.
- **Packs / abonnement** pour garages et concessions.
- **Options** : détourage, vraie 3D, rapport d'état.

**Point clé à chiffrer** : le coût de traitement par véhicule (stockage, calcul, éventuels services d'IA facturés à l'image) doit rester bien en dessous du prix de vente.

---

## 8. Intégration avec les annonces et les plateformes

Trois niveaux, du plus simple au plus ambitieux :

1. **Lien** collé dans la description de l'annonce. Fonctionne partout dès le début.
2. **Code d'intégration** (iframe) pour les sites de garages.
3. **API / partenariat** : la plateforme envoie le véhicule, reçoit le lien de visite. C'est surtout une démarche commerciale, à envisager une fois que le produit a fait ses preuves.

---

## 9. Plan par phases (durées indicatives)

| Phase | Contenu | Durée indicative |
|---|---|---|
| **0. Compréhension** | Tester les démos gratuites de 2-3 concurrents, noter forces et faiblesses | 1 semaine |
| **1. Test terrain** | Faire 3-4 visites avec des outils existants sur de vrais véhicules, les montrer à des garages, demander s'ils paieraient | 1-2 semaines |
| **2. Protocole de capture** | Définir la distance, la hauteur, la lumière, la façon de filmer. Produire un court tutoriel | 1 semaine |
| **3. Prototype du visualiseur** | Spin + intérieur 360° + lien unique | 2 semaines |
| **4. Espace utilisateur** | Envoi de la vidéo, traitement, lien, paiement | 3-4 semaines |
| **5. Pilote** | 3 à 5 garages et quelques particuliers. Critère : ils y arrivent seuls | 2 semaines |
| **6. Ouverture** | Code d'intégration, discussions avec des plateformes d'annonces | à définir |

Les phases 0 et 1 permettent de vérifier l'intérêt réel **avant** d'investir dans le développement.

### État d'avancement

- Fait : cadrage, comparaison de la concurrence, premiers prototypes (visualiseur et capture guidée).
- En cours : mise en ligne en https (GitHub + Vercel) pour tester la caméra.
- À venir : test sur un vrai véhicule, puis décision sur le rendu (spin simple, défauts cliquables, vraie 3D).

### Ce que les prototypes ne font pas encore
- Vrai **lien de partage** : pour l'instant la visite s'enregistre en fichier HTML autonome. Un lien web demande un stockage des images (piste : Supabase) et un compte à créer.
- Flèches **avance / recule** et **décale à gauche / à droite** : elles demandent de reconnaître le véhicule dans l'image (sa taille à l'écran indique la distance). Un petit modèle de détection dans le navigateur est une piste, à mesurer côté poids de chargement.
- Guidage vocal (« ralentis », « continue ») pour ne pas avoir à regarder l'écran.
- Photo 360° de l'intérieur avec points cliquables.
- Floutage de plaque, points sur les défauts, lien de partage, comptes, paiement.

---

## 10. Questions ouvertes

- Quelle cible en premier : particuliers, petits garages ou concessions ?
- Vidéo seule, ou vidéo + photos pour la qualité ?
- Le fond d'origine suffit-il, ou le détourage est-il indispensable pour convaincre ?
- Le spin suffit-il, ou la vraie 3D est-elle attendue par les clients ?
- Quel prix réel les gens acceptent-ils de payer ?
- Quel matériel demander : smartphone seul, ou caméra 360° (environ 300 €) ?
- Peut-on obtenir un premier échange avec une plateforme d'annonces ?
- **Le nom** : « autotour » est aussi un mot courant du tourisme (forfait voiture + hôtels). Le domaine a été indiqué comme libre, mais le nom peut prêter à confusion dans les recherches Google. À vérifier avant de s'y attacher : autres extensions (.fr, .com), marques déjà déposées (INPI), résultats de recherche.
- Réutiliser le même nom pour le dépôt, Vercel et le domaine, ou séparer nom de travail et nom commercial ?

---

## 11. Risques à surveiller

- **Marge** : un prix trop bas face au coût de traitement.
- **Qualité de capture** : des vidéos mal filmées produisent de mauvais spins. Le guidage doit être très clair.
- **Reflets** : pour la vraie 3D, résultat incertain.
- **Concurrence** : acteurs installés, mais souvent plus chers.
- **Vie privée** : plaques, visages, lieux visibles sur les images.

---

## 12. Prochaines étapes possibles

1. Créer le dépôt GitHub et le projet Vercel, y mettre `capture-guidee.html`.
2. Tester la capture guidée en https sur un vrai véhicule, puis passer la vidéo dans `visite-360.html`.
3. Noter ce qui fonctionne ou non (fluidité, sens de rotation, images sautées, reflets).
4. Tester la piste B (défauts cliquables) et, en parallèle, la piste D (vraie 3D) sur ce même véhicule.
5. Montrer les résultats à quelques garages et particuliers.
6. Comparer 2-3 concurrents via leurs démos gratuites.
7. Revenir à ce document, mettre à jour les hypothèses, et décider de la suite (projet Next.js, comptes, paiement).

---

*Dernière mise à jour : 24 septembre 2026. À faire évoluer au fil des tests.*
