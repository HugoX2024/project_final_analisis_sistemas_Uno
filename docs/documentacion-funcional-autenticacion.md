# Documentacion funcional - Modulo de autenticacion y acceso por tenant

## Nombre del modulo

Autenticacion y acceso por tenant.

## Proposito

El modulo permite registrar usuarios, iniciar sesion, generar tokens JWT, consultar la sesion activa y cerrar sesion. Cada operacion se asocia a un tenant mediante la cabecera HTTP `X-Tenant-ID`, lo que permite separar la informacion por institucion u hospital.

## Actores principales

- Usuario del sistema.
- Frontend Vue.
- API Laravel.
- Middleware de tenant.
- Middleware JWT.

## Requisitos previos

- PHP 8.2 o superior.
- Composer.
- Node.js 20 o superior.
- npm.
- SQLite o una base de datos compatible configurada en `.env`.

## Instalacion del proyecto

Desde la raiz del proyecto:

```bash
composer install
npm install
cp .env.example .env
php artisan key:generate
php artisan jwt:secret
```

Para desarrollo local con SQLite:

```bash
touch database/database.sqlite
php artisan migrate
php artisan db:seed
```

En Windows PowerShell, si no existe el archivo SQLite:

```powershell
New-Item -ItemType File -Path database\database.sqlite -Force
php artisan migrate
php artisan db:seed
```

## Ejecucion del proyecto

Terminal 1:

```bash
php artisan serve
```

Terminal 2:

```bash
npm run dev
```

URLs esperadas en desarrollo:

- Backend Laravel: `http://127.0.0.1:8000`
- Frontend Vite: `http://127.0.0.1:5173`
- Pantalla de login: `http://127.0.0.1:8000/login`

## Tenant demo

El seeder `TenantSeeder` crea un tenant demo:

```txt
00000000-0000-4000-8000-000000000001
```

Nombre:

```txt
Hospital General San Marcos (demo)
```

Este valor debe enviarse en la cabecera `X-Tenant-ID` cuando se usa el API.

## Rutas API del modulo

Todas las rutas estan bajo el prefijo `/api/v1`.

| Metodo | Ruta | Proteccion | Descripcion |
| --- | --- | --- | --- |
| POST | `/auth/register` | Tenant | Registra un usuario y devuelve token JWT. |
| POST | `/auth/login` | Tenant | Valida credenciales y devuelve token JWT. |
| GET | `/auth/me` | Tenant + JWT | Devuelve el usuario autenticado. |
| POST | `/auth/refresh` | Tenant + JWT refresh | Renueva el token JWT. |
| POST | `/auth/logout` | Tenant + JWT | Invalida el token actual. |

## Uso desde la interfaz

1. Abrir `http://127.0.0.1:8000/login`.
2. Ingresar el ID del tenant.
3. Ingresar correo y contrasena.
4. Presionar `Entrar`.
5. Si las credenciales son correctas, el sistema guarda el token JWT y redirige al inicio.

## Uso desde API

### Registrar usuario

```bash
curl -X POST http://127.0.0.1:8000/api/v1/auth/register \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: 00000000-0000-4000-8000-000000000001" \
  -d "{\"name\":\"Demo User\",\"email\":\"demo@example.com\",\"password\":\"password\",\"password_confirmation\":\"password\"}"
```

Respuesta esperada:

- Codigo HTTP `201`.
- Campo `access_token`.
- Campo `token_type` con valor `bearer`.
- Datos del usuario con roles y tenant.

### Iniciar sesion

```bash
curl -X POST http://127.0.0.1:8000/api/v1/auth/login \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: 00000000-0000-4000-8000-000000000001" \
  -d "{\"email\":\"demo@example.com\",\"password\":\"password\"}"
```

Respuesta esperada:

- Codigo HTTP `200`.
- Token JWT en `access_token`.
- Datos del usuario autenticado.

### Consultar usuario autenticado

Reemplazar `TOKEN_JWT` con el token recibido en login o registro.

```bash
curl -X GET http://127.0.0.1:8000/api/v1/auth/me \
  -H "Accept: application/json" \
  -H "X-Tenant-ID: 00000000-0000-4000-8000-000000000001" \
  -H "Authorization: Bearer TOKEN_JWT"
```

Respuesta esperada:

- Codigo HTTP `200`.
- Campo `user` con informacion del usuario, roles y tenant.

### Cerrar sesion

```bash
curl -X POST http://127.0.0.1:8000/api/v1/auth/logout \
  -H "Accept: application/json" \
  -H "X-Tenant-ID: 00000000-0000-4000-8000-000000000001" \
  -H "Authorization: Bearer TOKEN_JWT"
```

Respuesta esperada:

- Codigo HTTP `200`.
- Mensaje de cierre de sesion correcto.

## Validaciones funcionales

| Caso | Entrada | Resultado esperado |
| --- | --- | --- |
| Tenant existente | `X-Tenant-ID` valido | La peticion continua. |
| Tenant inexistente | `X-Tenant-ID` no registrado | Respuesta JSON de error. |
| Registro correcto | Nombre, correo y contrasena valida | Usuario creado y token JWT devuelto. |
| Login correcto | Correo y contrasena validos | Token JWT devuelto. |
| Login incorrecto | Contrasena incorrecta | Error de validacion. |
| Ruta protegida sin token | Sin `Authorization` | Error de autenticacion. |
| Ruta protegida con token | Bearer token valido | Respuesta con usuario autenticado. |
| Logout correcto | Token valido | Token invalidado. |

## Verificacion aplicada

Comandos ejecutados durante la preparacion y validacion:

```bash
npm run build
php artisan migrate --force
php artisan db:seed --force
php artisan route:list --path=api
php artisan test
```

Resultados:

- Build de frontend correcto.
- Migraciones aplicadas en SQLite.
- Seeders ejecutados con tenant y roles base.
- Rutas API del modulo registradas.
- Pruebas automatizadas aprobadas.
- Registro por API validado con respuesta JWT.

## Cambios realizados en el proyecto

En este sprint no se modifico la logica del modulo. Se agrego documentacion funcional para explicar como ejecutar, usar y verificar el modulo de autenticacion y acceso por tenant.

## Decision humana tomada

Se mantuvo el modulo existente sin alterar su comportamiento, porque la asignacion solicitada se enfoca en documentar un modulo funcional especifico y no en reemplazar su implementacion.

## Prompt usado

Mi modulo: Documentacion funcional del modulo. Documentar como usar, ejecutar y verificar un modulo funcional especifico.

## Objetivo del prompt

Definir el modulo a documentar y producir una guia funcional verificable para uso academico y revision del docente.

## Resumen de la respuesta recibida

Se propuso documentar el modulo de autenticacion y acceso por tenant, ya que es funcional, verificable y representa un flujo central del sistema.
