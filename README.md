# TaskFlow — Frontend

Interfaz React + Vite + Tailwind de TaskFlow, desplegada en **AWS Amplify Hosting**.

Proyecto final del curso de Cloud. Autor: Maycol Siqueros.

## URLs en produccion

| Recurso | URL |
|---|---|
| Aplicacion (Amplify) | https://main.d359qn409qmys2.amplifyapp.com |
| Backend (Elastic Beanstalk) | http://taskflow-msiqueros.us-east-2.elasticbeanstalk.com |
| Proxy HTTPS (CloudFront) | https://d2e9uff2frblnc.cloudfront.net |
| Repositorio del backend | https://github.com/MSiqueros/taskflow-backend |

## Arquitectura desplegada

```
Usuario
  |
  v
AWS Amplify Hosting  (este repositorio, React + Vite)
  |  /api/*  -> rewrite HTTPS
  v
Amazon CloudFront  (da HTTPS al backend)
  |  HTTP
  v
AWS Elastic Beanstalk  (Node.js + Express)
  |  MySQL 3306
  v
Amazon RDS  (MySQL 8.4)
```

## Personalizacion propia

- Nombre de la aplicacion: **TaskFlow — Maycol Siqueros**
- Paleta de marca cambiada de azul `#2563eb` a esmeralda `#059669`
- Textos de portada y cabecera adaptados al proyecto

## Configuracion en Amplify

| Ajuste | Valor |
|---|---|
| Comando de construccion | `npm run build` |
| Directorio de salida | `dist` |
| Archivo de build | `amplify.yml` (en la raiz del repo) |
| Variable de entorno | `VITE_API_URL = /api` |

### Reescritura hacia el backend

Amplify sirve el sitio por HTTPS. El entorno de Elastic Beanstalk de instancia unica
responde por HTTP, y el navegador bloquea ese contenido mixto.

Amplify ademas rechaza destinos http en sus reglas
(`BadRequestException: HTTP URLs cannot be used in custom rules`), y ACM no emite
certificados para `*.elasticbeanstalk.com`. Por eso hay una distribucion de
CloudFront delante del backend: aporta HTTPS gratuito en `*.cloudfront.net`.

Reglas de reescritura en Amplify (Hosting -> Rewrites and redirects):

| Origen | Destino | Tipo |
|---|---|---|
| `/api/<*>` | `https://d2e9uff2frblnc.cloudfront.net/api/<*>` | `200 (Rewrite)` |
| `/<*>` | `/index.html` | `200 (Rewrite)` |

La primera regla debe ir **antes** que la del SPA.

## Desarrollo local

```bash
npm install
cp .env.example .env     # VITE_API_URL=http://localhost:3000/api
npm run dev
```

El archivo `.env` esta excluido del control de versiones.
