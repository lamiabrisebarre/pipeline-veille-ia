# Pipeline de veille IA automatisée

Système complet d'automatisation de veille sur l'intelligence artificielle : génère automatiquement un briefing texte et un épisode podcast audio à partir de sources RSS, sans jamais retraiter le même article deux fois.

---

## Objectif

Automatiser entièrement la collecte, le filtrage, la synthèse et la diffusion de veille IA, en format texte (email HTML) et audio (podcast MP3) pour réduire le temps de traitement de l'information de plus de 80%.

---

## Stack technique

| Outil | Usage |
|---|---|
| **n8n** | Orchestration du workflow complet |
| **OpenAI GPT** | Synthèse et résumé des articles |
| **OpenAI TTS** | Génération audio (text-to-speech) |
| **Google Drive** | Stockage des épisodes podcast |
| **Notion** | Archivage des sources pour accès ultérieur |
| **Gmail** | Envoi du briefing email HTML |
| **Base de données n8n** | Mémoire des articles déjà traités |
| **RSS** | Collecte des sources IA |

---

## Architecture du pipeline

```
Schedule Trigger (CRON)
        ↓
Define Sources → RSS Read → Limit
        ↓
Filtre JS — articles déjà traités ? (mémoire BDD)
        ↓
Get many database pages (check Notion)
        ↓
Code JS — mise en forme des contenus
        ↓
        ├──────────────────────────────────┐
        ↓                                  ↓
  BRANCHE TEXTE                     BRANCHE AUDIO
Message → OpenAI (résumé)     Message → OpenAI (script podcast)
        ↓                                  ↓
  Code JS (formatage)            OpenAI TTS → Podcast MP3
        ↓                                  ↓
  Create Notion page             Upload Google Drive
        ↓                                  ↓
  Build Email HTML                         ↓
        └──────────────┬───────────────────┘
                       ↓
                 Send Briefing (Gmail)
                       ↓
                   Log & Done
```

---

## Fonctionnalités clés

**Mémoire anti-doublon**
Le pipeline vérifie en base de données les articles déjà traités avant chaque exécution, aucun article n'est jamais résumé deux fois, même si la source le republie sauf s'il y a des informations complémentaires intérressantes.

**Double sortie selon le format**
Deux branches parallèles indépendantes génèrent des sorties adaptées à chaque usage :
- Format **texte** → résumé structuré, envoyé par email HTML
- Format **audio** → script optimisé pour l'écoute, converti en MP3 via TTS et uploadé sur Google Drive

**Archivage Notion**
Chaque article traité est enregistré dans Notion avec ses métadonnées (source, date, résumé) pour un accès facilité à la base de connaissance constituée au fil du temps.

**Traçabilité**
Un nœud Log & Done enregistre chaque exécution pour faciliter le débogage et le suivi des performances.

---

## Résultats

- Temps de veille réduit de **plus de 80%**
- Briefing livré automatiquement par email chaque matin à 8h 
- Épisode podcast généré sans intervention manuelle
- Base de sources consultable à tout moment via Notion

---

## Contenu du repository

- `pipeline_veille_ia.json` — fichier d'export n8n 
- `photot_workflow_n8n.png` — vue complète du pipeline
- `photo_veille_ia_email.png` — exemple de rendu email

---

## Ce que ce projet m'a appris

- Concevoir une architecture d'automatisation multi-branches avec gestion d'état
- Intégrer plusieurs APIs (OpenAI, Notion, Google Drive, Gmail) dans un workflow cohérent
- Appliquer le prompt engineering pour obtenir des sorties différenciées selon le format cible (texte vs audio)
- Gérer la mémoire et la déduplication dans un pipeline de données récurrentes
