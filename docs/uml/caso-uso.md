# Diagrama de caso de uso

## Modulo de autenticacion y acceso por tenant

```mermaid
flowchart LR
    usuario["Usuario del sistema"]
    sistema["Sistema HIS"]

    registrar(("Registrarse"))
    login(("Iniciar sesion"))
    me(("Consultar usuario autenticado"))
    refresh(("Renovar token"))
    logout(("Cerrar sesion"))
    tenant(("Enviar X-Tenant-ID"))

    usuario --> registrar
    usuario --> login
    usuario --> me
    usuario --> refresh
    usuario --> logout

    registrar --> tenant
    login --> tenant
    me --> tenant
    refresh --> tenant
    logout --> tenant

    registrar --> sistema
    login --> sistema
    me --> sistema
    refresh --> sistema
    logout --> sistema
```

## Descripcion

El usuario interactua con el modulo para registrarse, iniciar sesion, consultar su informacion, renovar el token y cerrar sesion. Todas las operaciones dependen del tenant enviado mediante `X-Tenant-ID`.

