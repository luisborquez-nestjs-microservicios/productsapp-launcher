# Products App
Proyecto con arquitectura de microservicios construida con NestJS, utilizando NATS para comunicación entre servicios y un gateway HTTP para exponer endpoints a los clientes.

Contiene:
- [client-gateway](client-gateway): gateway/cliente que expone endpoints HTTP y se comunica por NATS.
- [products-ms](products-ms): microservicio de productos (Prisma + SQLite en desarrollo).
- [orders-ms](orders-ms): microservicio de órdenes (Postgres + Prisma).
- [payments-ms](payments-ms): microservicio de pagos (integra Stripe; expone endpoints relacionados con el flujo de pago).
- [auth-ms](auth-ms): microservicio de autenticación (gestión de usuarios, JWT y persistencia en MongoDB).

# Dev
Requisitos
---------
- Docker y Docker Compose instalados.
- Node.js y npm (opcional, si desea ejecutar servicios fuera de contenedores).

Ejecución en modo desarrollo (recomendada)
-----------------------------------------
1. Copie el archivo de ejemplo de variables de entorno si todavía no existe:
    ```
    cp .env.template .env
    ```
    (Windows: copiar manualmente o usar PowerShell `Copy-Item`)

2. Inicializar y actualizar sub-módulos de Git (si es la primera vez que clona el repositorio):
    ```
    git submodule update --init --recursive
    ```

3. Levante los servicios con Docker Compose (se compilan y arranca en modo desarrollo según `docker-compose.yml`):
    ```
    docker-compose up --build
    ```
    - Esto levantará `nats-server`, `client-gateway`, `products-ms`, `orders-ms`, `payments-ms`, `auth-ms` y la base de datos `orders-db`.
    - Los servicios montan los directorios `src` para recarga en caliente (`npm run start:dev`).

Ejecución manual de un servicio (sin Docker)
-------------------------------------------
1. Abra una terminal en la carpeta del servicio (por ejemplo `client-gateway`):
    ```
    cd client-gateway
    ```

2. Instale dependencias e inicie en modo desarrollo:
    ```
    npm install
    npm run start:dev
    ```

Notas importantes
-----------------
- Configure las variables en `.env` (puede partir de `.env.template`). El archivo `.env.template` incluye variables para Stripe y para la conexión de `auth-ms`.
- `orders-ms` usa PostgreSQL en `orders-db` cuando se levanta con Docker Compose; si ejecuta `orders-ms` fuera de Docker, asegúrese de apuntar `POSTGRES_URL` a una base de datos Postgres accesible.
- `payments-ms` requiere las variables de Stripe (`PAYMENTS_MS_STRIPE_SECRET`, `PAYMENTS_MS_STRIPE_WEBHOOK_SECRET`, `PAYMENTS_MS_STRIPE_SUCCESS_URL`, `PAYMENTS_MS_STRIPE_CANCEL_URL`) en el `.env` para funcionar correctamente.
- `auth-ms` requiere `AUTHMS_AUTHDB_URL` (URL de MongoDB) y `AUTHMS_JWT_SECRET` configuradas en el `.env`.
- Los puertos por defecto están en `.env` y `docker-compose.yml`. El gateway expone `CLIENT_GATEWAY_PORT` (por defecto `3000`).

Estructura del repositorio
--------------------------
- `client-gateway/` — gateway HTTP y controladores.
- `products-ms/` — microservicio de productos (Prisma + SQLite en dev).
- `orders-ms/` — microservicio de órdenes (Prisma + Postgres).
- `payments-ms/` — microservicio de pagos (Stripe).
- `auth-ms/` — microservicio de autenticación (MongoDB + JWT).

Ejecución rápida (comandos útiles)
--------------------------------
En Windows PowerShell:

```powershell
Copy-Item .env.template -Destination .env
docker-compose up --build
```

Ejecución local de un servicio (ejemplo `products-ms`):

```bash
cd products-ms
npm install
npm run start:dev
```

Notas finales
-------------
Mantenga los submódulos actualizados si los usa y compruebe las variables de entorno antes de levantar los servicios. Si necesita levantar únicamente un subconjunto de servicios (por ejemplo sólo el gateway y productos), adapte `docker-compose` o arranque los servicios manualmente desde sus carpetas.

## Pasos para crear los Git Submodules

1. Crear un nuevo repositorio en GitHub
2. Clonar el repositorio en la máquina local
3. Añadir el submodule, donde `repository_url` es la url del repositorio y `directory_name` es el nombre de la carpeta donde quieres que se guarde el sub-módulo (no debe de existir en el proyecto)
```
git submodule add <repository_url> <directory_name>
```
4. Añadir los cambios al repositorio (git add, git commit, git push)
Ej:
```
git add .
git commit -m "Add submodule"
git push
```
5. Inicializar y actualizar Sub-módulos, cuando alguien clona el repositorio por primera vez, debe de ejecutar el siguiente comando para inicializar y actualizar los sub-módulos
```
git submodule update --init --recursive
```
6. Para actualizar las referencias de los sub-módulos
```
git submodule update --remote
```

## Importante
Si se trabaja en el repositorio que tiene los sub-módulos, **primero actualizar y hacer push** en el sub-módulo y **después** en el repositorio principal. 

Si se hace al revés, se perderán las referencias de los sub-módulos en el repositorio principal y tendremos que resolver conflictos.

# Prod
1. Clonar repositorio
2. Crear archivo <i>.env</i> en base a <i>.env.template</i>
3. Configurar las variables de entorno necesarias
4. Ejecutar el comando
```
docker compose -f docker-compose.prod.yml up
```
