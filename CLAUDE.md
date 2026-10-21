# CNS Avis GMB
## Stack
Next.js (App Router, TypeScript) sur Vercel · Supabase (Postgres + Auth Google) · Vercel Cron · API Claude · Resend.
## Documents projet
Toujours lire ces documents avant de coder :
- @docs/PRD.md — pourquoi et quoi du produit
- @docs/PLAN.md — phases d'implémentation et user stories
- @docs/DESIGN.md — système design et direction visuelle
## Système design
Toujours lire DESIGN.md avant toute décision visuelle ou UI.
Polices, couleurs, espacements et direction esthétique y sont définis.
Ne pas dévier sans validation explicite.
En mode QA, signaler tout code qui ne respecte pas DESIGN.md.
## Conventions
- Tout calcul mensuel se fait en mois calendaire à l'heure de Paris (`Europe/Paris`), jamais en UTC.
- Tout import d'avis (zip ou API) est un upsert sur l'identifiant Google de l'avis par fiche, jamais un insert brut.
- Le parsing du zip Takeout reste confiné à un seul module d'import ; le reste du code ignore son format.
- Un avis n'est envoyé qu'une fois à l'API Claude pour l'analyse thématique, et déclenche au plus une alerte (aucune pour les avis du premier zip).
- Textes UI en français, au vouvoiement. Toute page et toute API exigent la session de l'unique email autorisé.
## Jargon métier
- **Fiche** : la fiche Google My Business de l'entreprise (une seule dans le périmètre).
- **Palier** : le dixième de note affiché par Google (4,8 → 4,9) ; c'est la base du simulateur.
- **Non classés** : avis analysés sans aucune mention de thème. Ce n'est pas un 11ᵉ thème.
