# LBC Smart Agent

Bot Telegram qui surveille Leboncoin et m'alerte quand une annonce de voiture mérite vraiment d'être regardée.

## Pourquoi

Je cherchais une voiture d'occasion. Après quelques jours à scroller LBC matin et soir, j'ai laissé tomber. Trop d'annonces sans intérêt pour mon profil, et les vraies bonnes affaires partent en quelques heures.

J'avais besoin d'un outil qui tourne en arrière-plan et qui ne me ping que quand ça vaut le coup. Avec une contrainte : ce qui est une bonne affaire pour un particulier prudent n'est pas la même chose que pour un préparateur qui cherche une perle à remettre en état.

## Architecture

Polling LBC toutes les 5 minutes, puis pipeline IA en deux étages :

1. **Haiku, en texte seul.** Passe le titre, prix, kilométrage, année et description. ~0,001 € par annonce. Élimine les disqualifications évidentes selon le profil. Selon le profil, 5 à 50 % des annonces survivent.
2. **Sonnet, multimodal.** Sur les survivantes, lit les photos (rouille, accidents, état réel), consulte une base de connaissances locale sur le modèle visé (problèmes connus, prix de marché), rédige une analyse argumentée. ~0,04 € par annonce.

L'étage 1 sert à protéger l'étage 2. Sans lui, surveiller des centaines d'annonces par jour avec Sonnet coûterait 10 à 20 fois plus cher.

## Trois profils d'acheteur

| Profil | Description | Sélectivité Haiku |
|---|---|---|
| Coup de fusil | Attend la perle sous-cotée | ~5 à 10 % |
| Particulier prudent | Fiabilité d'abord | ~20 à 25 % |
| Garage / Pro | Achète pour réparer et revendre | ~40 à 50 % |

Le même catalogue d'annonces est filtré différemment. Une voiture à embrayage fatigué est éliminée pour un particulier prudent, gardée pour un garage.

## Stack

Playwright pour le scraping, Anthropic SDK (Haiku 4.5 + Sonnet 4.6), bot Telegram pour les alertes, SQLite pour la persistance et le suivi du budget journalier. Onboarding conversationnel via un agent dédié.

## Pourquoi le code n'est pas public

Le scraping de Leboncoin est en zone grise vis-à-vis de leurs CGU. C'est un outil personnel, je préfère ne pas le distribuer.
