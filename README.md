# 🎵 Laravel Vue Starter Kit - Application de Gestion de Playlists

Application web moderne de gestion de playlists musicales construite avec **Laravel 12** et **Vue 3** (Inertia.js).

## 🚀 Installation

### Prérequis

- PHP 8.2 ou supérieur
- Composer
- Node.js 18+ et npm/pnpm
- MySQL (Laragon recommandé) ou SQLite/PostgreSQL

### Étapes d'installation

1. **Cloner le projet et installer les dépendances**

```bash
cd cours-iim-08-12-2025
composer install
npm install
```

2. **Configurer l'environnement**

```bash
cp .env.example .env
php artisan key:generate
```

3. **Configurer la base de données**

Le projet est configuré pour MySQL (Laragon). La base de données par défaut est `laravue`. Configurez le fichier `.env` :

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravue
DB_USERNAME=root
DB_PASSWORD=
```

**Alternative SQLite** (si vous préférez) :

```bash
touch database/database.sqlite
```

Puis dans `.env` :

```env
DB_CONNECTION=sqlite
DB_DATABASE=database/database.sqlite
```

4. **Exécuter les migrations et seeders**

```bash
php artisan migrate
php artisan db:seed
```

5. **Créer le lien symbolique pour le stockage**

```bash
php artisan storage:link
```

Cette commande est **essentielle** pour que les fichiers audio et images soient accessibles via `/storage`.

6. **Lancer l'application**

```bash
# Option 1 : Lancer tout en une commande (recommandé)
composer dev

# Option 2 : Lancer séparément
php artisan serve
npm run dev
```

L'application sera accessible sur `http://localhost:8000`

## 👤 Comptes par défaut

Après avoir exécuté `php artisan db:seed`, vous pouvez vous connecter avec :

- **Email** : `admin@gmail.com`
- **Mot de passe** : `admin123`
- **Rôle** : Administrateur

## 📡 API REST

### Authentification

L'API utilise un système de clés API. Pour utiliser l'API, vous devez :

1. Vous connecter à l'application web
2. Aller dans la section "Mes clés API"
3. Créer une nouvelle clé API (nom minimum 3 caractères)
4. Utiliser cette clé dans le header `x-api-key` de vos requêtes

### Endpoints disponibles

#### GET /api/playlists

Récupère toutes les playlists de l'utilisateur authentifié via la clé API.

**Headers requis :**

```
x-api-key: votre-clé-api-ici
```

**Exemple de requête avec cURL :**

```bash
curl -X GET http://localhost:8000/api/playlists \
  -H "x-api-key: votre-clé-api-ici" \
  -H "Accept: application/json"
```

**Exemple de requête avec JavaScript (fetch) :**

```javascript
fetch('http://localhost:8000/api/playlists', {
    method: 'GET',
    headers: {
        'x-api-key': 'votre-clé-api-ici',
        Accept: 'application/json',
    },
})
    .then((response) => response.json())
    .then((data) => console.log(data));
```

**Réponse JSON :**

```json
[
    {
        "id": 1,
        "slug": "ply-123e4567-e89b-12d3-a456-426614174000",
        "user_id": 1,
        "title": "Ma Playlist Favorie",
        "created_at": "2025-12-08T10:00:00.000000Z",
        "updated_at": "2025-12-08T10:00:00.000000Z",
        "tracks": [
            {
                "id": 1,
                "slug": "trk-123e4567-e89b-12d3-a456-426614174001",
                "title": "Titre de la musique",
                "artist": "Nom de l'artiste",
                "image": "tracks/images/trk-xxx.jpg",
                "audio": "tracks/musics/trk-xxx.mp3",
                "is_visible": true,
                "play_count": 0,
                "pivot": {
                    "playlist_id": 1,
                    "track_id": 1,
                    "play_count": 0,
                    "created_at": "2025-12-08T10:00:00.000000Z",
                    "updated_at": "2025-12-08T10:00:00.000000Z"
                }
            }
        ]
    }
]
```

**Codes de réponse :**

- `200` : Succès
- `401` : Clé API manquante ou invalide

## 🏗️ Architecture

### Backend (Laravel)

- **Framework** : Laravel 12
- **Authentification** : Laravel Fortify (avec 2FA)
- **API** : REST avec authentification par clé API
- **Base de données** : SQLite (par défaut) ou MySQL/PostgreSQL
- **Stockage** : Fichiers locaux (disque `public`)

### Frontend (Vue 3)

- **Framework** : Vue 3 avec TypeScript
- **Routing** : Inertia.js
- **Styling** : Tailwind CSS 4
- **UI Components** : Reka UI

### Modèles de données

- **User** : Utilisateurs avec rôle admin
- **Track** : Pistes musicales (gérées par les admins)
- **Playlist** : Playlists appartenant aux utilisateurs
- **ApiKey** : Clés API pour l'authentification REST

## 🔒 Sécurité

- ✅ Routes CRUD des Tracks protégées par middleware `IsAdmin`
- ✅ API protégée par middleware `CheckApiKey` (header `x-api-key`)
- ✅ Prévention du lazy loading activée (`Model::preventLazyLoading`)
- ✅ Validation des données avec Form Requests
- ✅ Clés API générées avec `Str::random(40)`
- ✅ URLs utilisant des slugs au lieu d'IDs (`getRouteKeyName()`)

## 🎯 Fonctionnalités

- ✅ Gestion de playlists musicales
- ✅ CRUD des tracks (réservé aux admins)
- ✅ Système de clés API pour l'authentification REST
- ✅ Authentification avec 2FA (Laravel Fortify)
- ✅ Compteur de lectures par piste dans les playlists
- ✅ Upload de fichiers audio et images
- ✅ Liens symboliques pour l'accès aux fichiers via `/storage`

## 🧪 Tests

```bash
php artisan test
```

## 📝 Commandes utiles

```bash
# Créer le lien symbolique pour le stockage
php artisan storage:link

# Réinitialiser la base de données
php artisan migrate:fresh --seed

# Lancer les tests
php artisan test

# Formater le code
composer pint
npm run format
```

## 🐛 Dépannage

### Les fichiers audio/images ne s'affichent pas

Assurez-vous d'avoir exécuté :

```bash
php artisan storage:link
```

### Erreur "Lazy loading violation"

C'est normal ! Le projet a activé `Model::preventLazyLoading()` pour détecter les problèmes N+1. Utilisez `with()` ou `load()` pour charger les relations.

### Erreur de validation "name too short"

Le nom des clés API doit contenir au minimum 3 caractères.

## 📚 Technologies utilisées

- Laravel 12
- Vue 3 + TypeScript
- Inertia.js
- Tailwind CSS 4
- Laravel Fortify
- Laravel Sanctum
- Pest (tests)

## 👨‍💻 Auteur

Projet développé dans le cadre du cours d'Antoine Drsl.

---

**Note** : N'oubliez pas d'exécuter `php artisan storage:link` après l'installation pour que les fichiers soient accessibles !
