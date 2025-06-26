Back- & Front-end: Laravel 11 API + Angular 20 Admin-Usuarios

Table of contents
|                              |                                                       |
| ---------------------------- | ----------------------------------------------------- |
| **1. Stack & prerequisites** | Node ≥ 20 · PHP ≥ 8.3 · Composer ≥ 2 · MSSQL · Git |
| **2. Backend (Laravel)**     | Installation · .env · Migrate/Seed · API docs         |
| **3. Frontend (Angular)**    | Installation · dev server · prod build                |
| **4. Project structure**     | Folders & noteworthy files                            |
| **5. Useful commands**       | Most-used npm / artisan shortcuts                     |


1 · Stack & prerequisites

| Tool                | Purpose                    | Version used |
| ------------------- | -------------------------- | ------------ |
| **Laravel**         | REST API + auth (Sanctum)  | 11.x         |
| **PHP**             | Runtime                    | ≥ 8.3        |
| **Node / npm**      | Angular CLI & tooling      | ≥ 20 / ≥ 10  |
| **Angular**         | Web app (standalone, lazy) | 20.x         |
| **Material 2**      | UI components              | 20.x         |
| **MSSQL**| Database                   |        |

2 · Backend – laravel-api/
2.1 Install & set-up

git clone https://github.com/your-org/admin-usuarios.git
cd admin-usuarios/laravel-api
composer install
cp .env.example .env
php artisan key:generate        # sets APP_KEY

Edit .env DB section:
DB_CONNECTION=sqlsrv
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=prueba
DB_USERNAME=root
DB_PASSWORD=secret

2.2 Migrations & seeders

php artisan migrate            # creates tables
php artisan db:seed            # seeds Departamentos, Cargos, Users

Seed data matches the example given:

| Departamento                 | Cargo                                                   | Usuario (ej.)           |
| ---------------------------- | ------------------------------------------------------- | ----------------------- |
| Tecnología de la Información | Administrador / Líder Frontend / Desarrollador Frontend | ppicapiedra • pmarmol … |
| Legal                        | Abogado                                                 | sgoodman • kwexler      |
| Seguridad                    | Guardia                                                 | jalimaña                |
| Eventos y Buffers            | Pollero                                                 | jpinkman                |


2.3 Run dev server
php artisan serve --port=8000
# API base → http://localhost:8000/api

2.4 Auth flow (Sanctum)
| Endpoint           | Method | Body                    | Description                                                                            |
| ------------------ | ------ | ----------------------- | -------------------------------------------------------------------------------------- |
| `/api/auth/login`  | POST   | `{ usuario, password }` | Returns `{ token, user }` if credentials **and** `idDepartamento == 1` (administrador) |
| `/api/auth/logout` | POST   | –                       | Revokes token                                                                          |

Include header for protected routes:

Authorization: Bearer <token>

| Verb       | URI                  | Query / Body                                              | Notes                                 |
| ---------- | -------------------- | --------------------------------------------------------- | ------------------------------------- |
| **GET**    | `/api/usuarios`      | `page`, `search`, `departamentoId`, `cargoId`, `per_page` | Paginated list                        |
| **POST**   | `/api/usuarios`      | `usuario, email, password, …`                             | Creates (password hashed via mutator) |
| **GET**    | `/api/usuarios/{id}` | –                                                         | Single user                           |
| **PUT**    | `/api/usuarios/{id}` | Fields to update                                          |                                       |
| **DELETE** | `/api/usuarios/{id}` | –                                                         |                                       |


3 · Frontend – admin-usuarios/ (Angular 20)

3.1 Install dependencies
cd admin-usuarios
npm ci        # or npm install

3.2 Run dev server
npm start           # ng serve
# App → http://localhost:4200

3.3 Build production

npm run build       # artifacts in dist/

3.4 App modules & routing
| Path              | Feature                                | Guard                                      |
| ----------------- | -------------------------------------- | ------------------------------------------ |
| `/login`          | LoginComponent (standalone)            | —                                          |
| `/admin-usuarios` | Lazy feature (`UsuariosListComponent`) | AuthGuard (checks `token` in localStorage) |

3.5 Key Angular pieces
| File                                     | Responsability                            |
| ---------------------------------------- | ----------------------------------------- |
| `services/auth.service.ts`               | login(), logout(), token()                |
| `services/usuarios.service.ts`           | CRUD + filters (signals)                  |
| `interceptors/auth-token.interceptor.ts` | Adds `Authorization` header               |
| `interceptors/http-error.interceptor.ts` | Global API error → MatSnackBar            |
| `guards/auth.guard.ts`                   | Redirects to `/login` if no token         |
| `usuarios-list/*`                        | MatTable + server paginator / filters     |
| `usuario-form/*`                         | MatDialog + reactive form + server errors |
| `confirm-dialog/*`                       | Re-usable yes/no dialog                   |

Material theme: Purple/Amber, plus custom corporate palette in styles/scss/theme/_colors.scss.

4 · Useful commands cheat-sheet
| Purpose           | Backend                      | Frontend        |
| ----------------- | ---------------------------- | --------------- |
| Install deps      | `composer install`           | `npm ci`        |
| Dev server        | `php artisan serve`          | `npm start`     |
| Tests             | `php artisan test`           | `ng test`       |
| Lint              | –                            | `ng lint`       |
| Prod build        | –                            | `npm run build` |
| DB migrate / seed | `php artisan migrate --seed` | –               |
| Generate key      | `php artisan key:generate`   | –               |

5. Ingreso al sitio:

user: pmarmol
Password: password
