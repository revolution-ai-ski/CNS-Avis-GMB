# Plan : Analyse des avis Google My Business

> PRD source : `docs/PRD.md`

## Décisions architecturales

Décisions durables qui s'appliquent à toutes les phases :

- **Stack** : Next.js (App Router, TypeScript) hébergé sur Vercel. Base Postgres et authentification Supabase. Tâche planifiée Vercel Cron pour la récupération quotidienne. Interface en français, conçue pour un écran d'ordinateur large.
- **Routes** :
  - `/connexion` : connexion avec Google
  - `/import` : dépôt du zip Google Takeout et choix de la fiche
  - `/bilan` : redirige vers le mois calendaire écoulé
  - `/bilan/AAAA-MM` : bilan d'un mois (note, tendance, thèmes, point prioritaire, réponses, simulateur)
  - `/bilan/AAAA-MM/themes/<theme>` : avis d'un thème, avec le passage mis en évidence
  - `/bilan/AAAA-MM/pdf` : téléchargement du PDF du bilan
  - `/demandes-avis` : lien, QR code, modèles SMS et mail
  - `/fiche` : connexion de la fiche Google et état de la récupération automatique
- **Schema** (Postgres) :
  - `compte` : email Google autorisé, jeton d'accès aux avis (chiffré), scopes accordés
  - `fiches` : identifiant Google de la fiche, nom de l'entreprise, lien de dépôt d'avis, date de la dernière récupération réussie, statut de la dernière récupération (ok / échec + motif)
  - `avis` : fiche, identifiant Google de l'avis (**unique par fiche**, clé de dédoublonnage), prénom de l'auteur, étoiles (1 à 5), texte (vide possible), date de publication, texte et date de la réponse (vides possibles), lien vers l'avis sur Google, source (`zip` | `api`), date d'analyse thématique (vide = pas encore analysé)
  - `mentions_theme` : avis, thème, polarité (`positif` | `negatif`), passage cité. Un avis analysé sans mention compte dans « Non classés ».
  - `brouillons` : avis, texte courant (modifications de l'artisan conservées), date de génération
  - `alertes` : avis, date d'envoi. **Un envoi au plus par avis.**
- **Modèles clés** : Fiche, Avis, MentionTheme, Brouillon, Alerte.
- **Thèmes** (enum figé, 10 valeurs) : `rapidite-intervention`, `rdv-honore`, `ponctualite`, `qualite-intervention`, `proprete-chantier`, `prix-devis`, `explications-conseils`, `communication-client`, `certificat-demarches`, `amabilite-professionnalisme`. « Non classés » n'est pas un thème : ce sont les avis analysés qui n'ont aucune mention.
- **Règles de calcul** :
  - Les mois sont calendaires, à l'heure de Paris.
  - Note d'un mois = moyenne des étoiles de tous les avis publiés jusqu'à la fin de ce mois. Elle s'affiche au dixième (arrondi comme Google), avec la valeur au centième en plus petit.
  - Thèmes et point prioritaire : sur les 12 mois glissants qui se terminent au mois affiché.
  - Délai de réponse : en jours.
- **Authentification et autorisation** : connexion Google via Supabase Auth, réservée à un seul email autorisé fixé par configuration. Toute page et toute API exigent cette session. Le scope d'accès aux avis (`business.manage`) est demandé au même compte Google, seulement au moment de « Connecter ma fiche ».
- **Frontières des services tiers** :
  - Google OAuth
  - Google Business Profile API : lecture des avis et des réponses. Accès à faire valider par Google, plusieurs semaines de délai.
  - API Claude : classement des avis par thème et rédaction des brouillons
  - Resend : mails d'alerte et d'échec
  - Le format du zip Takeout est isolé derrière un seul point d'import, car il peut changer sans préavis.

---

## Phase 1 : Premier bilan depuis un zip

**User stories** : US-1, US-10, US-13

### Ce qu'on livre

L'artisan se connecte avec son compte Google. Tout autre compte est refusé. Il dépose le zip de son export Takeout, qui ne contient qu'une seule fiche. Les avis sont enregistrés et il arrive sur `/bilan`, qui affiche sa note telle que Google l'affiche et son nombre total d'avis.

### Critères d'acceptation

- [ ] Un compte Google autre que l'email autorisé ne peut accéder à aucune page ni à aucune API.
- [ ] Après le dépôt d'un zip Takeout réel de 131 avis, le bilan affiche 131 avis et la même note que la fiche Google (dixième + centième).
- [ ] Le bilan s'affiche en moins de 30 secondes après le dépôt.
- [ ] Les avis sont stockés avec leur identifiant Google, leurs étoiles, leur texte, leur date, leur réponse éventuelle et leur lien.

## Bloquée par

Aucune — démarrable immédiatement

---

## Phase 2 : Demandes d'avis

**User stories** : US-35, US-36, US-37

### Ce qu'on livre

La page `/demandes-avis` propose :
- le lien direct vers la page de dépôt d'avis de la fiche, avec un bouton « Copier » ;
- un QR code téléchargeable, assez net pour l'impression ;
- deux modèles, un SMS court et un mail, au vouvoiement et signés du nom de l'entreprise, modifiables puis copiables.

### Critères d'acceptation

- [ ] « Copier » met le lien de dépôt d'avis dans le presse-papiers.
- [ ] Le QR code, imprimé puis scanné avec un téléphone, ouvre la page de dépôt d'avis de la fiche.
- [ ] Les modèles SMS et mail se modifient sur place et se copient en un clic.

## Bloquée par

- Phase 1

---

## Phase 3 : Chargement robuste du zip

**User stories** : US-2, US-3, US-4

### Ce qu'on livre

Pendant le chargement, une progression est visible. Un zip illisible ou qui n'est pas un export Google My Business affiche « Ce fichier n'est pas un export Google My Business », avec la marche à suivre pour obtenir le bon export. Un export sans avis mène à un écran vide explicite qui renvoie vers `/demandes-avis`.

### Critères d'acceptation

- [ ] Une progression est affichée du dépôt jusqu'à l'ouverture du bilan.
- [ ] Un zip corrompu et un zip quelconque (non Takeout) affichent tous deux le message et la marche à suivre, sans rien enregistrer.
- [ ] Un export sans avis affiche l'écran vide avec un lien vers les demandes d'avis.

## Bloquée par

- Phase 1
- Phase 2 (cible du lien de l'écran vide)

---

## Phase 4 : Plusieurs fiches et rechargement sans doublon

**User stories** : US-5, US-9

### Ce qu'on livre

Si le zip contient plusieurs fiches, l'artisan choisit celle à analyser avant l'import. Recharger un zip n'ajoute que les avis inconnus et met à jour les réponses des avis déjà connus. Aucun avis n'est jamais compté deux fois.

### Critères d'acceptation

- [ ] Un zip à plusieurs fiches propose la liste des fiches. Seule la fiche choisie est importée.
- [ ] Charger deux fois le même zip laisse le nombre total d'avis inchangé.
- [ ] Un zip plus récent ajoute uniquement les nouveaux avis et met à jour les réponses publiées depuis.

## Bloquée par

- Phase 1

---

## Phase 5 : Bilan du mois

**User stories** : US-11, US-12, US-15, US-16, US-17

### Ce qu'on livre

`/bilan` ouvre le mois calendaire écoulé. Un sélecteur permet d'ouvrir n'importe quel mois passé (`/bilan/AAAA-MM`). Le bilan montre :
- la répartition des avis par étoiles ;
- le nombre d'avis reçus dans le mois, comparé au mois précédent ;
- un message explicite, avec une incitation à demander des avis, si aucun avis n'est arrivé dans le mois.

### Critères d'acceptation

- [ ] Sans réglage, le bilan affiché est celui du mois écoulé (heure de Paris).
- [ ] Le sélecteur ouvre un mois passé et met tous les indicateurs à jour.
- [ ] La répartition 1★ à 5★ totalise le nombre d'avis à la fin du mois affiché.
- [ ] Le nombre d'avis du mois et celui du mois précédent sont affichés côte à côte.
- [ ] Un mois sans nouvel avis affiche le message dédié, avec un lien vers les demandes d'avis.

## Bloquée par

- Phase 1

---

## Phase 6 : Évolution de la note sur 12 mois

**User stories** : US-14

### Ce qu'on livre

Le bilan montre l'évolution de la note affichée sur les 12 mois qui se terminent au mois affiché, mois par mois.

### Critères d'acceptation

- [ ] La valeur de chaque mois est la note calculée sur tous les avis publiés jusqu'à la fin de ce mois.
- [ ] La valeur du mois affiché est égale à la note affichée en tête du bilan.

## Bloquée par

- Phase 5

---

## Phase 7 : Simulateur de note

**User stories** : US-30, US-31, US-32

### Ce qu'on livre

Le bilan affiche « Il vous faut N avis 5★ pour afficher X,X » (palier au-dessus) et « Vous pouvez recevoir N avis 1★ avant de descendre à X,X » (palier en dessous). À 5,0, seule la marge est affichée.

### Critères d'acceptation

- [ ] N est le plus petit nombre d'avis 5★ qui fait afficher le dixième supérieur.
- [ ] N est le plus grand nombre d'avis 1★ qui garde le dixième actuel affiché.
- [ ] Les deux valeurs sont vérifiées par des tests sur des cas limites (4,8 ; 4,85 ; 4,95 ; 5,0).
- [ ] À 5,0, la phrase « Il vous faut… » n'apparaît pas.

## Bloquée par

- Phase 1

---

## Phase 8 : Suivi des réponses

**User stories** : US-23, US-24, US-29 (et le bouton « Ouvrir l'avis sur Google » de US-28)

### Ce qu'on livre

Le bilan affiche :
- la part des avis ayant reçu une réponse ;
- le délai moyen de réponse, en jours ;
- la liste des avis sans réponse, triés par note croissante puis du plus ancien au plus récent, chacun avec « Ouvrir l'avis sur Google » ;
- un message explicite quand tous les avis ont une réponse.

### Critères d'acceptation

- [ ] Un avis compte comme répondu dès qu'une réponse est publiée sur Google.
- [ ] Le tri de la liste respecte la note croissante, puis l'ancienneté.
- [ ] « Ouvrir l'avis sur Google » ouvre l'avis concerné.
- [ ] Sans avis en attente, le message « vous êtes à jour » remplace la liste.

## Bloquée par

- Phase 1

---

## Phase 9 : Brouillons de réponse

**User stories** : US-25, US-26, US-27, US-28

### Ce qu'on livre

Chaque avis sans réponse a un brouillon :
- au vouvoiement, avec le prénom du client tel qu'il apparaît sur Google et le rappel du point précis de l'avis ;
- signé du nom de l'entreprise ;
- pour un avis négatif : remercie le client, reconnaît le problème et propose un échange, sans jamais contester.

Le brouillon se modifie sur place et ses modifications sont conservées. Il dispose de trois boutons : « Copier », « Autre proposition » et « Ouvrir l'avis sur Google ».

### Critères d'acceptation

- [ ] 100 % des avis sans réponse ont un brouillon.
- [ ] « Copier » copie le texte courant, modifications comprises, en un clic.
- [ ] « Autre proposition » remplace le brouillon par une nouvelle rédaction différente.
- [ ] Sur un échantillon d'avis négatifs, aucun brouillon ne conteste le client.

## Bloquée par

- Phase 8

---

## Phase 10 : Thèmes clients

**User stories** : US-18, US-19, US-20

### Ce qu'on livre

Les avis sont analysés une fois chacun et rattachés aux 10 thèmes, en positif ou en négatif, avec le passage cité. Un même avis peut relever de plusieurs thèmes.

Le bilan montre, pour chaque thème, les mentions positives et négatives sur les 12 mois glissants, ainsi que le compteur « Non classés ». Ouvrir un thème (`/bilan/AAAA-MM/themes/<theme>`) liste les avis concernés, avec le passage mis en évidence.

### Critères d'acceptation

- [ ] Les 10 thèmes sont toujours affichés, même ceux à 0.
- [ ] Un avis peut être positif sur un thème et négatif sur un autre.
- [ ] Les avis sans texte ou trop vagues apparaissent dans « Non classés », avec leur nombre.
- [ ] Le détail d'un thème met en évidence le passage pertinent de chaque avis.
- [ ] Un avis déjà analysé n'est pas réanalysé à chaque ouverture du bilan.

## Bloquée par

- Phase 5

---

## Phase 11 : Point prioritaire

**User stories** : US-21, US-22

### Ce qu'on livre

Le bilan désigne le thème le plus souvent reproché sur les 12 mois glissants. En cas d'égalité, c'est celui dont le reproche est le plus récent. Les avis qui le justifient sont affichés avec lui. Sans aucun reproche, un message indique qu'aucun point faible n'apparaît et renvoie vers les demandes d'avis.

### Critères d'acceptation

- [ ] Le thème choisi est celui qui a le plus de mentions négatives sur la période.
- [ ] L'égalité est départagée par la date du reproche le plus récent (testé).
- [ ] Sans reproche, le message dédié s'affiche avec un lien vers `/demandes-avis`.

## Bloquée par

- Phase 10

---

## Phase 12 : Connexion de la fiche et récupération quotidienne

**User stories** : US-6, US-7

### Ce qu'on livre

Depuis `/fiche`, l'artisan connecte sa fiche une fois pour toutes. Une récupération automatique tourne au moins une fois par jour : elle ajoute les nouveaux avis, met à jour les réponses et lance l'analyse des nouveaux avis. Le bilan affiche la date de la dernière récupération.

### Critères d'acceptation

- [ ] La connexion de la fiche réutilise le compte Google déjà connecté et ne lui demande que l'accès aux avis.
- [ ] Un avis publié sur la fiche apparaît dans l'outil dans les 24 heures, sans recharger de zip.
- [ ] Un avis déjà importé par zip n'est pas compté une seconde fois.
- [ ] La date de la dernière récupération réussie est visible dans le bilan.

## Bloquée par

- Phase 4 (dédoublonnage)
- Dépendance externe : accès à la Google Business Profile API accordé par Google

---

## Phase 13 : Échec de la récupération

**User stories** : US-8

### Ce qu'on livre

Quand la récupération automatique échoue (accès révoqué, erreur Google), un bandeau s'affiche dans l'outil et un mail est envoyé à l'adresse du compte. Le message invite à reconnecter la fiche ou à recharger un zip. Les avis déjà récupérés restent consultables.

### Critères d'acceptation

- [ ] Un échec simulé affiche le bandeau, avec les liens vers `/fiche` et `/import`.
- [ ] Un mail d'échec est envoyé, une seule fois par série d'échecs.
- [ ] Le bilan reste consultable avec les données existantes.
- [ ] Le bandeau disparaît après la récupération réussie suivante.

## Bloquée par

- Phase 12

---

## Phase 14 : Alertes mail pour les avis de 1 à 3★

**User stories** : US-33, US-34

### Ce qu'on livre

Chaque nouvel avis de 1 à 3★ récupéré déclenche un mail à l'adresse du compte. Le mail contient les étoiles, le texte de l'avis, le brouillon de réponse et le lien vers l'avis. Les avis 4 et 5★ ne déclenchent rien.

### Critères d'acceptation

- [ ] Un avis de 1 à 3★ publié sur la fiche déclenche un mail reçu dans les 24 heures.
- [ ] Un avis 4 ou 5★ ne déclenche aucun mail.
- [ ] Un même avis ne déclenche jamais deux mails, même s'il est récupéré plusieurs fois.
- [ ] Les avis importés lors du premier chargement d'un zip ne déclenchent pas de mail.

## Bloquée par

- Phase 9
- Phase 12

---

## Phase 15 : PDF du bilan

**User stories** : US-38

### Ce qu'on livre

Un bouton « Télécharger le PDF » produit le bilan du mois affiché : indicateurs, évolution de la note, thèmes, point prioritaire et liste des avis sans réponse, sans les brouillons. Le nom du fichier contient le mois du bilan.

### Critères d'acceptation

- [ ] Le PDF se télécharge en un clic depuis le bilan affiché.
- [ ] Il contient tous les indicateurs du bilan à l'écran et aucun brouillon.
- [ ] Le nom du fichier contient le mois (AAAA-MM).

## Bloquée par

- Phase 5
- Phase 6
- Phase 8
- Phase 10
- Phase 11
