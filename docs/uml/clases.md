# Diagrama de clases

## Modulo de autenticacion y acceso por tenant

```mermaid
classDiagram
    class User {
        +int id
        +string tenant_id
        +string name
        +string email
        +string password
        +getJWTIdentifier()
        +getJWTCustomClaims()
        +tenant()
        +roles()
    }

    class Tenant {
        +string id
        +string name
        +string slug
        +array data
    }

    class AuthController {
        +register(Request request) JsonResponse
        +login(Request request) JsonResponse
        +me(Request request) JsonResponse
        +refresh(Request request) JsonResponse
        +logout(Request request) JsonResponse
    }

    class TenantMiddleware {
        +handle(Request request, Closure next)
    }

    class JwtAuth {
        +handle(Request request, Closure next)
    }

    class AuthStore {
        +token
        +tenantId
        +user
        +setTenantId(tenantId)
        +persistSession(payload)
        +login(credentials)
        +logout()
        +clearSession()
    }

    class AxiosPlugin {
        +baseURL
        +requestInterceptor()
        +responseInterceptor()
    }

    User --> Tenant : pertenece a
    AuthController --> User : crea y autentica
    AuthController --> Tenant : usa tenant resuelto
    TenantMiddleware --> Tenant : valida existencia
    JwtAuth --> User : valida token
    AuthStore --> AxiosPlugin : consume API
    AxiosPlugin --> AuthController : envia solicitudes HTTP
```

## Descripcion

El backend concentra la logica de autenticacion en `AuthController`, usando `User`, `Tenant`, `TenantMiddleware` y `JwtAuth`. El frontend usa `AuthStore` y el plugin Axios para guardar tenant, token y usuario en el navegador.

