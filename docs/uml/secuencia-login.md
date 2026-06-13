# Diagrama de secuencia

## Flujo de inicio de sesion

```mermaid
sequenceDiagram
    actor Usuario
    participant LoginPage as Pantalla Login Vue
    participant AuthStore as Store Auth Pinia
    participant Axios as Axios Plugin
    participant TenantMiddleware as TenantMiddleware
    participant AuthController as AuthController
    participant User as Modelo User
    participant JWT as JWTAuth

    Usuario->>LoginPage: Ingresa tenant, correo y contrasena
    LoginPage->>AuthStore: setTenantId(tenantId)
    LoginPage->>AuthStore: login(credentials)
    AuthStore->>Axios: POST /auth/login
    Axios->>TenantMiddleware: Envia X-Tenant-ID
    TenantMiddleware->>TenantMiddleware: Valida tenant existente
    TenantMiddleware->>AuthController: Continua solicitud
    AuthController->>User: Busca usuario por tenant y correo
    User-->>AuthController: Devuelve usuario o null
    AuthController->>AuthController: Verifica contrasena
    AuthController->>JWT: Genera token JWT
    JWT-->>AuthController: access_token
    AuthController-->>Axios: Respuesta JSON con token y usuario
    Axios-->>AuthStore: Retorna datos
    AuthStore->>AuthStore: Guarda token y usuario en localStorage
    AuthStore-->>LoginPage: Login correcto
    LoginPage-->>Usuario: Redirige a inicio
```

## Descripcion

El inicio de sesion requiere que el frontend envie el tenant en la cabecera `X-Tenant-ID`. Laravel valida el tenant, busca el usuario dentro de ese tenant, verifica la contrasena y genera un token JWT para la sesion.

