# Spec — Améliorations landing page mariage (Cibele & Benny)

Date : 2026-07-15
Fichier concerné : `Siteweb/Joao/index.html`

## Contexte

Version de départ : copie du HTML fourni par Jack (artiste), avec RSVP par `mailto:` remplacé par `cibelleetbenny@gmail.com`. Cette spec couvre 4 améliorations validées en brainstorming avec l'utilisateur, avant implémentation.

## 1. RSVP → Google Form (au lieu de mailto)

Le formulaire visible sur le site reste stylé comme aujourd'hui (carte "carte d'embarquement"), mais au clic sur "Oui, je serai là", les données sont envoyées en arrière-plan vers un Google Form existant — pas de redirection vers un client mail.

**Mécanisme** : formulaire HTML caché (`target` pointant vers un `<iframe>` invisible, `action` = endpoint `formResponse` du Google Form) rempli en JS puis soumis programmatiquement. Cette technique évite les soucis CORS (c'est une soumission de formulaire classique, pas un `fetch`).

**Endpoint** : `https://docs.google.com/forms/d/e/1FAIpQLSfrssXtKOKuM2iahBxVmUosm16J9sd4pkyqW_I3bKuOd_dA8A/formResponse`

**Mapping des champs** :
| Champ site | Entry ID Google Form |
|---|---|
| Nom | `entry.1529032957` |
| Compagnon/conjoint(e) | `entry.1781680325` |
| Enfant(s) (nombre) | `entry.805436237` |
| Adultes supplémentaires (nombre) | `entry.458276541` |

**Limite connue** : Google Forms ne renvoie pas de réponse lisible (soumission opaque). Le site affiche le tampon "Embarquement confirmé" de façon optimiste après soumission, sans confirmation serveur — comportement identique à la version mailto actuelle.

**Suppression** : le lien `mailto:` et toute la logique associée sont retirés.

## 2. Structure des invités (remplace la liste dynamique "+ Ajouter une personne")

Le formulaire RSVP passe de "nom + liste illimitée de noms" à 4 champs fixes :

1. **Votre nom** — texte, obligatoire
2. **Compagnon/conjoint(e)** — texte, optionnel (placeholder "Prénom (optionnel)")
3. **Enfant(s)** — compteur numérique avec boutons `−` / `+`, plage 0–6, défaut 0
4. **Adultes supplémentaires** (parents, etc.) — compteur numérique `−` / `+`, plage 0–6, défaut 0

Une ligne récapitulative légère affiche le total de personnes calculé (1 + compagnon éventuel + enfants + adultes suppl.), pour que l'invité puisse vérifier avant d'envoyer.

Le bloc "+ Ajouter une personne" / lignes dynamiques / bouton de suppression (`extra-guest-row`, `add-guest-btn`, `remove-guest-btn`) est supprimé du HTML, CSS et JS existants.

## 3. Compte à rebours (urgence RSVP, pas hype événement)

Positionné près de la section RSVP (pas dans le hero), affiche le temps restant jusqu'au **31/12/2026 23:59**, cadré comme une urgence à agir : ex. "Il reste **X jours** pour confirmer votre présence".

- CSS/JS pur (pas de librairie), mise à jour simple (ex. toutes les heures suffit, pas besoin de la seconde près)
- Respecte `prefers-reduced-motion` (pas d'anim si l'utilisateur la désactive)
- Hors scope de cette version : compte à rebours vers le jour J (27/07/2027) avec actus/météo — prévu comme itération séparée après le 1er janvier 2027

## 4. Cartes lieux (section "Informations pratiques")

Remplace le paragraphe placeholder "Informations pratiques" par 3 cartes séparées (même style que les `.tag` existants), une par lieu : **Cérémonie**, **Vin d'honneur**, **Dîner**.

Chaque carte contient :
- Titre du lieu
- Mini-carte intégrée gratuite (`<iframe src="https://www.google.com/maps?q=ADRESSE&output=embed">`, pas de clé API)
- Adresse en texte (placeholder Mindelo pour l'instant, à remplacer quand les vraies adresses seront connues)
- Bouton **"↗ Itinéraire"** → lien Google Maps (`https://www.google.com/maps/search/?api=1&query=ADRESSE_ENCODÉE`)
- Bouton **"Waze"** → lien Waze (`https://waze.com/ul?q=ADRESSE_ENCODÉE&navigate=yes`)

Les 3 adresses sont des placeholders explicitement marqués comme provisoires, à mettre à jour plus tard.

## Hors scope (à ne pas faire maintenant)

- Compte à rebours vers le jour du mariage (itération future)
- Détails supplémentaires sur les invités (allergies, régime, âges précis des enfants)
- Carte Google Maps interactive avec API JS / clé API
- Tout service tiers payant (Formspree, etc.)
