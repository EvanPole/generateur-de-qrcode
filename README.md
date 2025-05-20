

# Documentation

---

## 📦 Prérequis

- PHP 8.2+
- Composer
- Laravel 12
- Une base de données (MySQL, PostgreSQL, SQLite…)
- Node.js & npm (pour compiler les assets, si nécessaire)

---

## 🚀 Installation

1. **Cloner le dépôt :**
   ```bash
   git clone https://github.com/votre-utilisateur/laravel-qr.git
   cd laravel-qr
   ```

2. **Installer les dépendances PHP :**

   ```bash
   composer install
   ```

3. **Configurer l’environnement :**

   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

   * Éditez `.env` pour renseigner vos identifiants de base de données.

4. **Migrer la base de données :**

   ```bash
   php artisan migrate
   ```

5. **(Optionnel) Compiler les assets :**

   ```bash
   npm install
   npm run dev
   ```

6. **Lancer le serveur de développement :**

   ```bash
   php artisan serve
   ```

---

## 🔗 Routes

### Publiques

| Méthode | URI          | Nom de route      | Description                                                |
| ------- | ------------ | ----------------- | ---------------------------------------------------------- |
| `GET`   | `/`          | `qrcode.acceuil`  | Page d’accueil                                             |
| `GET`   | `/generate`  | `qrcode.generate` | Formulaire de génération de QR Code                        |
| `GET`   | `/qr/{uuid}` | `qr.redirect`     | Redirection vers l’URL du QR Code & enregistrement du scan |

### Authentification

* Routes générées par `Auth::routes()`
* Accès à `/home` redirige vers le dashboard (`home`).

### Protégées (middleware `auth`)

| Méthode  | URI                  | Nom de route                 | Contrôleur                   | Action                     |
| -------- | -------------------- | ---------------------------- | ---------------------------- | -------------------------- |
| `GET`    | `/admin`             | `home.admin.dashboard`       | `AdminController@index`      | Affiche le dashboard admin |
| `GET`    | `/admin/generateur`  | `generateur.admin.dashboard` | `AdminController@generateur` | Liste des QR Codes         |
| `GET`    | `/dashboard/qr`      | `qrcode.panel`               | `QRCodeController@index`     | Liste & stats de vos QR    |
| `POST`   | `/dashboard/qr`      | `qrcode.store`               | `QRCodeController@store`     | Création d’un nouveau QR   |
| `DELETE` | `/dashboard/qr/{id}` | `qrcode.destroy`             | `QRCodeController@destroy`   | Suppression d’un QR        |
| `PUT`    | `/qrcode/{id}`       | `qrcode.update`              | `QRCodeController@update`    | Mise à jour d’un QR        |

---

## 🛠 Contrôleurs & Fonctions

### `MainController`

* `acceuil()`
  Affiche la vue `resources/views/acceuil.blade.php`.

* `qrcode()`
  Affiche la vue `resources/views/qrcode-gen.blade.php`.

* `test()`
  Affiche la vue `resources/views/admin/generateur.blade.php`.

### `AdminController`

* `index()`

  * Récupère tous les QR Codes de l’utilisateur connecté avec le nombre total de scans.
  * Compile les statistiques des 7 derniers jours.
  * Rend la vue `resources/views/admin/home.blade.php`.

* `generateur()`

  * Liste les QR Codes de l’utilisateur pour la génération.
  * Rend la vue `resources/views/admin/generateur.blade.php`.

### `QRCodeController`

* `index()`

  * Liste les QR Codes de l’utilisateur et agrège les scans par date.
  * Rend la vue `home.admin.dashboard`.

* `store(Request $request)`

  * Valide les champs (`destination_url`, options de style…).
  * Crée une nouvelle entrée `QRCode` avec un `uuid` unique.
  * Redirige vers le dashboard avec un message de succès.

* `update(Request $request, $id)`

  * Valide l’URL et le titre facultatif.
  * Met à jour le QR Code correspondant à l’utilisateur.
  * Redirige vers le dashboard avec un message de succès.

* `destroy($id)`

  * Supprime le QR Code si l’utilisateur est propriétaire.
  * Redirige avec un message de confirmation.

---

## 🗄 Modèles

* **`App\Models\QRCode`**

  * `id`, `uuid`, `title`, `destination_url`, `user_id`, options (taille, couleurs, forme, padding…).
  * Relation: `hasMany('App\Models\Scan')`.

* **`App\Models\Scan`**

  * `id`, `qrcode_id`, `user_agent`, `ip`, `created_at`.
  * Relation: `belongsTo('App\Models\QRCode')`.

---

## 🎨 Vues

Toutes les vues se trouvent dans `resources/views` :

```
.
├── acceuil.blade.php
├── qrcode-gen.blade.php
└── admin
    ├── home.blade.php
    └── generateur.blade.php
```

---

## 📈 Statistiques

* À chaque visite de `/qr/{uuid}`, un `Scan` est enregistré, puis l’utilisateur est redirigé vers la `destination_url`.
* Le dashboard affiche :

  * Nombre total de scans par QR Code.
  * Évolution quotidienne des scans (graphique).




Cette documentation couvre l’installation, la description des routes, des contrôleurs, modèles, vues et le workflow principal. N’hésitez pas à l’adapter en fonction de vos besoins (ajout d’exemples d’API, capture d’écran du dashboard, etc.).
```
