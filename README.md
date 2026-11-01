# biblioteca-gateway

El API Gateway del proyecto guía de DSY1107, **tal como queda al terminar L8**, en su propio repositorio.
NestJS, puerto **8080**. Verifica el access token contra el JWKS de Cognito y responde **401** sin uno válido.
Es la única puerta pública: el cliente nunca supo que existen los puertos 3000, 3001 y 3002.

| Archivo | Qué hace | Qué exige |
|---|---|---|
| `src/libros.controller.ts` | `GET` y `POST /v1/libros`, reenvía a `LIBROS_URL` | token válido y scope `biblioteca/libros.leer` (`.escribir` en el `POST`) |
| `src/prestamos.controller.ts` | `GET /v1/prestamos`, reenvía a `PRESTAMOS_URL` | token válido, scope `biblioteca/libros.leer` y grupo `bibliotecarios` |
| `src/panel.controller.ts` | `/v1/panel...`, reenvía al BFF con la cabecera `Authorization` | token válido y scope `biblioteca/libros.leer` |
| `src/auth/verificador.ts` | `verificar()`, `tieneScope()`, `estaEnGrupo()` | `iss`, firma, `exp`, `token_use: access` y `client_id` |

## Cómo usarlo

Necesitas **Node 24.15.0 o superior**. Los mismos comandos sirven en Windows (PowerShell), macOS y Linux,
uno por línea:

```bash
cd $HOME/DSY1107/l9/biblioteca-gateway
npm install
```

Copia el `.env.example` a `.env` (Windows: `Copy-Item .env.example .env`; macOS y Linux: `cp .env.example .env`) y
rellena `COGNITO_ISSUER` y `COGNITO_CLIENT_ID` con los de tu ficha. Después:

```bash
npm run start:dev
```

Sin Docker necesita al lado el BFF (3000), `biblioteca-libros` (3001) y `biblioteca-prestamos` (3002). Con todo el
sistema, desde `biblioteca-plataforma`: `docker compose up -d --build --wait`.

## Cómo se comprueba

```bash
curl.exe -i http://localhost:8080/v1/libros
curl.exe -i -H "Authorization: Bearer <TOKEN_LECTOR>" http://localhost:8080/v1/libros
```

En macOS y Linux, `curl` sin el `.exe`. La primera da **401**; la segunda, **200** con el catálogo.

`docs/https-y-wireshark.md` guarda aparte el HTTPS del paso 2.5 de L1, que no va en el `main.ts` normal.
