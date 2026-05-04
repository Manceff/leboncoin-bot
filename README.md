# LBC Smart Agent

> Agent IA conversationnel qui surveille Leboncoin en continu, analyse les annonces automobiles via un pipeline en deux étages — filtrage économique puis analyse approfondie — et notifie sur Telegram lorsqu'une vraie bonne affaire correspond au profil de l'acheteur.

---

## En une lecture diagonale

- **Origine** — projet personnel, né d'une recherche concrète de voiture d'occasion
- **Surveillance** — polling Leboncoin toutes les cinq minutes
- **Pipeline IA** — Haiku filtre les annonces (~0,001 €/annonce), Sonnet analyse les survivantes en profondeur (~0,04 €/annonce)
- **Sortie** — alerte Telegram avec analyse argumentée, pas de score chiffré
- **Particularité** — le raisonnement s'adapte au profil de l'acheteur (trois profils précâblés)

---

## Pourquoi ce projet

J'étais en recherche d'une voiture d'occasion. Au bout de quelques jours à scroller manuellement Leboncoin matin et soir, j'ai constaté trois choses : la quasi-totalité des annonces sont sans intérêt pour mon profil ; les vraies opportunités partent en quelques heures ; et la fatigue de tri me faisait passer à côté de bonnes affaires par démotivation.

Le besoin est apparu naturellement — un outil qui surveille en continu, qui filtre intelligemment, et qui ne notifie que lorsque quelque chose mérite réellement l'attention. Avec une nuance importante : la définition d'une *bonne affaire* n'est pas la même selon le profil de l'acheteur. Un particulier prudent qui veut une voiture fiable n'a pas les mêmes critères qu'un préparateur qui cherche une perle sous-cotée à remettre en état. Le bot devait donc raisonner contextuellement plutôt que d'appliquer un score universel.

---

## Architecture en deux étages

Le pipeline tire parti de la différence de coût entre les modèles Claude pour rendre la surveillance économiquement viable :

**Étage 1 — Haiku, en texte seul.** Pour chaque annonce remontée, un appel à Haiku passe le titre, le prix, le kilométrage, l'année et la description. Coût marginal : environ 0,001 € par annonce. Le rôle de Haiku est d'éliminer les disqualifications évidentes — incohérence de prix, défauts rédhibitoires mentionnés en clair, modèles hors périmètre. Selon le profil de l'utilisateur, entre 5 % et 50 % des annonces survivent à ce filtre.

**Étage 2 — Sonnet, multimodal et augmenté.** Sur les annonces survivantes, un appel à Sonnet effectue une analyse approfondie : lecture des photos pour détecter rouille, accidents probables, état réel ; consultation d'une base de connaissances locale spécifique au modèle visé (problèmes connus, fourchette de prix de marché) ; rédaction d'une analyse argumentée. Coût marginal : environ 0,04 € par annonce.

Cette architecture permet de surveiller plusieurs centaines d'annonces par jour pour quelques euros, là où analyser chacune avec Sonnet coûterait dix à vingt fois plus. C'est le type d'arbitrage de coût qui devient critique dès qu'un agent IA fonctionne en continu.

---

## Les trois profils d'acheteur

Le bot embarque trois profils précâblés, sélectionnés par l'utilisateur lors d'un onboarding conversationnel :

| Profil | Description | Sélectivité Haiku |
|---|---|---|
| **Coup de fusil** | Attend la perle sous-cotée en parfait état | Ultra-strict (~5-10 %) |
| **Particulier prudent** | Veut rouler sans surprise, fiabilité d'abord | Strict (~20-25 %) |
| **Garage / Pro** | Achète pour réparer et revendre | Permissif (~40-50 %) |

Le même catalogue d'annonces est traité différemment selon le profil. Un véhicule à embrayage fatigué sera éliminé pour un particulier prudent et conservé pour un garage qui peut le remettre en état pour quelques centaines d'euros.

---

## Stack

| Couche | Outil |
|---|---|
| Surveillance Leboncoin | Scraper Python avec Playwright |
| Pipeline IA | Anthropic SDK (Haiku 4.5 + Sonnet 4.6) |
| Notifications | Bot Telegram |
| Persistance | SQLite (annonces vues, budget journalier) |
| Onboarding | Agent conversationnel dédié |
| Connaissances locales | Markdown indexé, retrieval simple |

---

## Pourquoi le code n'est pas public

Le scraping de Leboncoin se situe en zone grise au regard des conditions générales d'utilisation de la plateforme. Le projet est un outil personnel, je préfère ne pas le rendre exécutable par un tiers. Le présent dépôt documente l'approche, l'architecture et les arbitrages ; le code source reste en local.

---

## Auteur

**Mancef Ferrah** — étudiant en master, candidat à un stage d'AI Engineer.
Projet personnel, conçu et développé en autonomie.
