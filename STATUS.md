# État Actuel du Projet (ATM - At The Moment)

Le projet **LearnWithAnime** est actuellement en phase de démarrage / scaffolding.

## Ce qui est en place
* **Structure globale :**
  * Monorepo séparé en deux dossiers distincts : `frontend/` (Vue 3) et `backend/` (Rust).
  * Fichier `docker-compose.yml` présent pour lancer l'environnement de dev localement (ports 5173 pour le front, 3000 pour le back).
* **Frontend :**
  * Framework : Vue 3 avec TypeScript et Vite.
  * Dépendance clé intégrée : `@supabase/supabase-js` pour la gestion de l'authentification et de la base de données côté client.
  * Pages de base (scaffolding) : Un système de routing/transition basique est en place dans `App.vue` (`AuthPage.vue` vs `AiPlayground.vue` selon le statut de session).
* **Backend :**
  * Langage / Framework : Rust avec Axum (API HTTP asynchrone).
  * Base de données : SQLx configuré avec support Postgres et UUID.
  * Fonctionnalités (vues dans `main.rs`) : Il y a déjà des fondations pour :
    * La gestion des rôles (Admin).
    * La configuration des LLM par utilisateur (modèles, prompt système, température).
    * Un catalogue de données en mémoire (JSON) gérant les Decks, les Kanji (caractères, JLPT, radicaux).
* **Divers :**
  * GitHub Actions, configuration de linting (Prettier, rustfmt) théoriquement configurés (selon le TODO).

## Ce qui manque ou nécessite de l'attention (Critique)
* **Base de données Locale :** Le `docker-compose.yml` ne contient **pas** de service pour PostgreSQL ou Redis, pourtant indispensables au backend selon l'architecture prévue (`sqlx` côté backend attend une base). Si l'on repose entièrement sur Supabase (SaaS), le fichier docker-compose devrait l'indiquer ou le simuler localement (via Supabase CLI / Local dev).
* **Tests :** Le frontend a une commande `test` qui fait un simple `echo "No tests yet"`. Le backend ne semble pas avoir de dossier `tests/` très fourni pour l'instant.
* **Intégration Continue :** Le frontend utilise npm mais on trouve un `pnpm-lock.yaml` et `pnpm-workspace.yaml` à la racine, ce qui peut créer des conflits d'outils de packaging.

---

# Idées d'Amélioration & Prochaines Étapes

## 1. Architecture & Infrastructure
* **Clarifier la stratégie Base de données :** Puisque le frontend utilise Supabase mais que le backend Rust utilise SQLx (qui s'attend à du PostgreSQL classique), il faut s'assurer que le backend se connecte à la DB PostgreSQL fournie par Supabase.
* **Mettre à jour `docker-compose.yml` :** Ajouter une base de données PostgreSQL locale pour le développement hors-ligne, ou configurer le projet pour utiliser l'environnement local de Supabase (`supabase start`).
* **Unifier le gestionnaire de paquets :** Choisir entre `npm` et `pnpm` de manière définitive (le fichier racine utilise `pnpm-workspace.yaml`, mais le frontend a un `package-lock.json`).

## 2. Développement Backend (Rust / Axum)
* **API AI Centralisée :** Le TODO mentionne un évaluateur IA. Il serait bien de créer un trait (interface) en Rust pour facilement switcher entre OpenAI, Anthropic (Claude), ou un modèle local (Ollama).
* **Spaced Repetition System (SRS) :** C'est le cœur du projet. Implémenter un algorithme comme **FSRS** (Free Spaced Repetition Scheduler - meilleur que SM-2) en Rust.
* **Swagger/OpenAPI :** Ajouter `utoipa` pour générer automatiquement la documentation de l'API REST. Très utile pour que le frontend sache quels endpoints appeler.

## 3. Développement Frontend (Vue 3)
* **State Management :** Ajouter `Pinia` pour gérer l'état de l'application (session utilisateur, préférences anime, cache des cartes).
* **UI/UX :**
  * Intégrer un framework CSS léger et personnalisable comme `Tailwind CSS` (ou `UnoCSS`) pour aller plus vite sur la création des interfaces gamifiées.
  * Créer le composant de la "Flashcard" avec des animations de retournement (flip).
* **PWA (Progressive Web App) :** Configurer `vite-plugin-pwa` dès maintenant pour s'assurer que l'app soit installable sur mobile et fonctionne partiellement hors ligne.

## 4. Fonctionnalités "LearnWithAnime" (Core Business)
* **Système de "Contextualisation" :** Permettre à l'utilisateur de lier un mot de vocabulaire à un moment précis d'un anime. Le backend pourrait aller chercher (ou demander à l'IA de générer) la phrase d'exemple exacte correspondant à cet anime.
* **Audio Web Speech API :** Commencer à implémenter la reconnaissance vocale sur le frontend dans le `AiPlayground` pour que l'utilisateur s'entraîne à prononcer, pas juste à lire.

## 5. Qualité de Code (DevEx)
* Mettre en place **Vitest** sur le frontend pour les tests unitaires.
* Écrire des tests d'intégration en Rust sur les routes de l'API (avec une DB en mémoire ou une test DB).
