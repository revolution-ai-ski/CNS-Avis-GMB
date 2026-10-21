# PRD — Analyse des avis Google My Business

## Problème

L'artisan supprime ou rend inertes les cuves à fioul de ses clients. Sa fiche Google My Business compte 131 avis et affiche une note de 4,8 ou plus. C'est sa vitrine principale, et la note Google pèse directement sur sa renommée. Pourtant cette note stagne. Il ne sait pas quels aspects de son service la tirent vers le bas, ni quoi corriger en priorité.

À ce niveau, chaque avis négatif se voit et chaque dixième coûte cher : il faut de nombreux avis 5★ pour afficher 4,9, alors que quelques avis 1★ suffisent à redescendre à 4,7. Lire ses avis un par un ne lui donne ni tendance ni priorité. Il ne suit pas les avis restés sans réponse, et rien ne le prévient quand un avis négatif arrive. Il doit tout faire pour améliorer sa note, mais aujourd'hui il pilote à l'aveugle.

## Solution

Une fois par mois, l'artisan ouvre son bilan et voit en un coup d'œil :

- **Note et tendance** : la note affichée sur Google, son évolution mois par mois, la répartition par étoiles et le nombre d'avis reçus ce mois-ci comparé au mois précédent.
- **Thèmes clients** : pour chacun des 10 thèmes de son métier, ce que les clients apprécient ou reprochent, avec les avis concernés.
- **Point prioritaire** : le thème à travailler en priorité, avec les avis qui le justifient.
- **Réponses aux avis** : la part des avis ayant reçu une réponse, son délai de réponse et la liste des avis encore sans réponse, chacun avec un brouillon de réponse prêt à copier.
- **Simulateur de note** : combien d'avis 5★ il lui faut pour afficher le palier supérieur, et combien d'avis 1★ il peut encaisser avant de descendre d'un palier.
- **Demandes d'avis** : le lien direct vers sa fiche, un QR code à imprimer et des modèles de SMS et de mail.

Il démarre en chargeant le zip de son export Google Takeout, puis connecte sa fiche. Dès lors, l'outil récupère automatiquement les nouveaux avis et le prévient par mail dès qu'un avis de 1 à 3★ arrive, avec un brouillon de réponse. Il peut télécharger chaque bilan en PDF.

## Utilisateur cible

Un artisan indépendant qui travaille seul et dont le métier est de supprimer ou de neutraliser des cuves à fioul. Il passe ses journées sur chantier et a peu de temps pour la gestion. Il n'a aucune compétence en analyse de données. Il ouvre l'outil sur son ordinateur une fois par mois, le soir ou en fin de semaine, pour faire son bilan. Entre deux bilans, il reçoit les alertes par mail. Sa fiche Google My Business compte 131 avis, pour une note de 4,8 ou plus.

## User Stories

**Démarrage et récupération des avis**

- **US-1** En tant qu'artisan, je veux charger le zip de mon export Google Takeout, afin d'obtenir mon premier bilan sans autre manipulation.
- **US-2** En tant qu'artisan, je veux voir la progression pendant le chargement du zip, afin de savoir que l'outil travaille.
- **US-3** En tant qu'artisan, je veux un message clair si le zip n'est pas un export Google My Business ou s'il est illisible, afin de savoir quoi refaire.
- **US-4** En tant qu'artisan, je veux un écran explicite si mon export ne contient aucun avis, afin de comprendre la situation et d'accéder directement aux demandes d'avis.
- **US-5** En tant qu'artisan, je veux choisir la fiche à analyser si mon export en contient plusieurs, afin d'analyser la bonne.
- **US-6** En tant qu'artisan, je veux connecter ma fiche Google une fois pour toutes, afin que mes nouveaux avis soient récupérés automatiquement.
- **US-7** En tant qu'artisan, je veux voir la date de la dernière récupération des avis, afin de savoir si mon bilan est à jour.
- **US-8** En tant qu'artisan, je veux être prévenu dans l'outil et par mail si la récupération automatique échoue, afin de reconnecter ma fiche ou de recharger un zip.
- **US-9** En tant qu'artisan, je veux pouvoir recharger un zip sans que mes avis soient comptés deux fois, afin que mes indicateurs restent justes.
- **US-10** En tant qu'artisan, je veux être le seul à accéder à mon bilan, afin que les avis et les noms de mes clients restent privés.

**Bilan du mois, note et tendance**

- **US-11** En tant qu'artisan, je veux que le bilan s'ouvre par défaut sur le mois écoulé, afin de faire mon point mensuel sans réglage.
- **US-12** En tant qu'artisan, je veux pouvoir revoir le bilan d'un mois passé, afin de comparer avec un mois précis.
- **US-13** En tant qu'artisan, je veux voir ma note telle que Google l'affiche et mon nombre total d'avis, afin de connaître ma situation en un coup d'œil.
- **US-14** En tant qu'artisan, je veux voir l'évolution de ma note sur les 12 derniers mois, afin de savoir si elle progresse, stagne ou baisse.
- **US-15** En tant qu'artisan, je veux voir la répartition de mes avis par étoiles, afin de savoir combien d'avis non-5★ pèsent sur ma note.
- **US-16** En tant qu'artisan, je veux comparer le nombre d'avis reçus ce mois-ci à celui du mois précédent, afin de mesurer l'effet de mes demandes d'avis.
- **US-17** En tant qu'artisan, je veux un message explicite quand je n'ai reçu aucun nouvel avis dans le mois, afin de ne pas croire à une erreur et d'être incité à demander des avis.

**Thèmes clients et point prioritaire**

- **US-18** En tant qu'artisan, je veux voir, pour chacun des 10 thèmes, le nombre de mentions positives et négatives, afin de savoir ce que mes clients apprécient et ce qu'ils me reprochent.
- **US-19** En tant qu'artisan, je veux ouvrir un thème et lire les avis concernés, avec le passage pertinent mis en évidence, afin de comprendre précisément ce qui est dit.
- **US-20** En tant qu'artisan, je veux que les avis sans texte ou trop vagues soient regroupés en « Non classés », avec leur nombre, afin de savoir quelle part de mes avis n'apparaît dans aucun thème.
- **US-21** En tant qu'artisan, je veux voir le point prioritaire et les avis qui le justifient, afin de savoir quoi corriger en premier.
- **US-22** En tant qu'artisan, je veux un message explicite quand aucun thème ne m'est reproché, afin de savoir que mon effort doit porter sur le nombre d'avis plutôt que sur un défaut.

**Réponses aux avis**

- **US-23** En tant qu'artisan, je veux voir la part de mes avis ayant reçu une réponse et mon délai moyen de réponse, afin de mesurer ma réactivité.
- **US-24** En tant qu'artisan, je veux la liste de mes avis sans réponse, les plus négatifs en tête, afin de traiter d'abord ceux qui menacent ma note.
- **US-25** En tant qu'artisan, je veux un brouillon de réponse pour chaque avis sans réponse, au vouvoiement, avec le prénom du client, le rappel du point précis de son avis et ma signature, afin de répondre vite et sincèrement.
- **US-26** En tant qu'artisan, je veux modifier un brouillon avant de le copier, afin d'ajouter ma touche personnelle.
- **US-27** En tant qu'artisan, je veux demander une autre proposition de brouillon, afin d'en avoir un autre si le premier ne me convient pas.
- **US-28** En tant qu'artisan, je veux copier un brouillon en un clic puis ouvrir l'avis sur Google, afin de publier ma réponse sans chercher l'avis.
- **US-29** En tant qu'artisan, je veux un message explicite quand tous mes avis ont une réponse, afin de savoir que je suis à jour.

**Simulateur de note**

- **US-30** En tant qu'artisan, je veux savoir combien d'avis 5★ il me faut pour afficher le palier supérieur, afin de me fixer un objectif concret.
- **US-31** En tant qu'artisan, je veux savoir combien d'avis 1★ je peux encaisser avant de descendre d'un palier, afin de mesurer ma marge.
- **US-32** En tant qu'artisan dont la note affichée est 5,0, je veux que le simulateur indique seulement ma marge, afin de ne pas voir un objectif impossible.

**Alertes**

- **US-33** En tant qu'artisan, je veux recevoir un mail dès qu'un avis de 1 à 3★ est publié, avec l'avis, un brouillon de réponse et le lien vers l'avis, afin de répondre dans la journée.
- **US-34** En tant qu'artisan, je ne veux pas recevoir de mail pour les avis 4 et 5★, afin de ne pas être dérangé inutilement sur chantier.

**Demandes d'avis**

- **US-35** En tant qu'artisan, je veux copier le lien direct vers la page de dépôt d'avis de ma fiche, afin de l'envoyer à mes clients.
- **US-36** En tant qu'artisan, je veux télécharger un QR code imprimable menant à ma fiche, afin de le placer sur mon certificat ou ma carte de visite.
- **US-37** En tant qu'artisan, je veux des modèles de SMS et de mail de demande d'avis, modifiables et prêts à copier, afin de solliciter mes clients après l'intervention sans rédiger moi-même.

**PDF**

- **US-38** En tant qu'artisan, je veux télécharger le bilan du mois affiché en PDF, afin de l'archiver ou de le partager.

## Critères de succès

- Le bilan s'affiche en moins de 30 secondes après le chargement d'un zip Google Takeout de 131 avis.
- Le nombre total d'avis affiché par l'outil est égal à celui de la fiche Google (131 sur 131).
- La note affichée par l'outil est identique à celle affichée sur la fiche Google.
- Chaque avis sans réponse est accompagné d'un brouillon, copiable en 1 clic.
- Un avis de 1 à 3★ publié sur la fiche déclenche un mail reçu dans les 24 heures. Un avis 4 ou 5★ ne déclenche aucun mail.
- Un avis publié sur la fiche apparaît dans l'outil dans les 24 heures, sans rechargement de zip.
- Le PDF se télécharge en 1 clic et contient tous les indicateurs du bilan affiché.
- Le QR code, scanné avec un téléphone, ouvre la page de dépôt d'avis de la fiche.
- Dès le 2e bilan mensuel, 100 % des avis ont une réponse publiée sous 7 jours.
- La note affichée reste à 4,8 ou plus chaque mois, et atteint 4,9 dans les 12 mois suivant le premier bilan.

## Hors périmètre

- Publication des réponses directement sur Google depuis l'outil.
- Envoi des demandes d'avis par l'outil (SMS ou mails envoyés automatiquement, relances).
- Alertes par SMS ou par notification, et alertes pour les avis 4 et 5★.
- Comparaison avec des concurrents.
- Autres plateformes d'avis (Pages Jaunes, Trustpilot, Facebook…).
- Plusieurs fiches ou plusieurs artisans, et partage d'accès avec d'autres personnes.
- Version mobile.
- Archivage des PDF dans l'outil.
- Thèmes personnalisables ou découverts automatiquement.
- Saisie d'une note cible libre et simulation avec des avis fictifs.
- Signalement ou demande de suppression d'avis auprès de Google.
- Analyse d'avis rédigés dans une autre langue que le français.

## Décisions d'implémentation

- **Form factor** : application en français, consultée sur ordinateur et conçue pour un écran large.
- **Accès** : l'artisan se connecte avec son compte Google. Cette même connexion autorise la récupération automatique des avis de sa fiche.
- **Premier chargement** : un seul zip à la fois, déposé par glisser-déposer ou choisi dans un sélecteur, avec un indicateur de progression. Si le zip contient plusieurs fiches, l'artisan choisit celle à analyser.
- **Récupération automatique** : au moins une fois par jour. Un avis déjà connu, qu'il vienne d'un zip ou de la récupération automatique, n'est jamais compté deux fois. En cas d'échec, un bandeau s'affiche dans l'outil et un mail est envoyé ; les avis déjà récupérés restent consultables.
- **Erreurs de chargement** : un zip illisible ou qui n'est pas un export Google My Business affiche « Ce fichier n'est pas un export Google My Business », avec la marche à suivre pour obtenir le bon export. Un export sans avis affiche un écran vide explicite qui renvoie vers les demandes d'avis.
- **Période** : le bilan s'ouvre sur le mois calendaire écoulé. Un sélecteur permet de revoir n'importe quel mois passé. Chaque indicateur mensuel est comparé au mois précédent.
- **Note** : affichée au dixième, comme sur Google, avec la valeur au centième en plus petit. L'évolution couvre les 12 derniers mois.
- **Thèmes** : 10 thèmes fixes.
  1. Rapidité d'intervention : délai pour obtenir un rendez-vous.
  2. RDV honoré : l'artisan est venu, oui ou non.
  3. Ponctualité : il est arrivé à l'heure convenue.
  4. Qualité de l'intervention : pompage, dégazage, découpe ou neutralisation.
  5. Propreté du chantier.
  6. Prix et devis.
  7. Explications et conseils : ce qu'il explique de l'intervention elle-même.
  8. Communication avec le client : il est facile à joindre, confirme le rendez-vous, prévient en cas de retard, suit le client après l'intervention.
  9. Certificat et démarches : attestation pour l'assurance ou la vente du bien.
  10. Amabilité et professionnalisme.

  Un même avis peut relever de plusieurs thèmes, en positif sur l'un et en négatif sur un autre. Les avis sans texte ou impossibles à classer vont dans « Non classés », avec un compteur visible.
- **Point prioritaire** : le thème le plus souvent reproché sur les 12 derniers mois. En cas d'égalité, c'est celui dont le reproche est le plus récent. S'il n'y a aucun reproche, un message indique qu'aucun point faible n'apparaît et renvoie vers les demandes d'avis.
- **Réponses** : un avis compte comme répondu dès qu'une réponse est publiée sur Google. Le délai de réponse est exprimé en jours. Les avis sans réponse sont triés par note croissante, puis du plus ancien au plus récent.
- **Brouillons** : au vouvoiement, avec le prénom du client tel qu'il apparaît sur Google et le rappel du point précis de l'avis, signés du nom de l'entreprise tel qu'il figure sur la fiche. Pour un avis négatif, le brouillon remercie le client, reconnaît le problème et propose un échange, sans jamais contester publiquement. Chaque brouillon se modifie sur place et dispose de trois boutons : « Copier », « Autre proposition » et « Ouvrir l'avis sur Google ».
- **Simulateur** : formulations « Il vous faut N avis 5★ pour afficher 4,9 » et « Vous pouvez recevoir N avis 1★ avant de descendre à 4,7 ». Les paliers sont le dixième affiché juste au-dessus et juste en dessous de la note actuelle. À 5,0, seule la marge est affichée.
- **Alertes** : un mail par avis de 1 à 3★, envoyé à l'adresse du compte Google connecté, dans les 24 heures suivant la publication. Il contient les étoiles, le texte de l'avis, le brouillon de réponse et le lien vers l'avis.
- **Demandes d'avis** : le lien direct vers la page de dépôt d'avis, avec un bouton « Copier ». Un QR code téléchargeable en image assez nette pour l'impression. Deux modèles, un SMS court et un mail, au vouvoiement et signés du nom de l'entreprise, modifiables avant la copie.
- **PDF** : un bouton « Télécharger le PDF » produit le bilan du mois affiché (indicateurs, thèmes, point prioritaire, liste des avis sans réponse), sans les brouillons. Le nom du fichier contient le mois du bilan.

## Notes complémentaires

- **Dépendance** : la récupération automatique passe par un accès aux avis que Google doit autoriser. Le délai d'obtention ne dépend pas de nous et peut prendre plusieurs semaines. Le zip reste donc la voie de démarrage.
- **Risque** : le contenu de l'export Google Takeout peut changer sans préavis.
- **Hypothèse** : une partie des avis contient seulement des étoiles, sans texte. Ils comptent dans la note mais dans aucun thème.
- **Risque** : un avis très court (« Top ! ») ne permet pas de savoir de quel thème il parle.
- **Hypothèse** : les avis contiennent le nom des clients, et ces données personnelles restent réservées à l'artisan.
