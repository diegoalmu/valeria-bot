# 🔒 Seguridad del Proyecto

## Reglas generales

- **NUNCA** subir credenciales, tokens o API keys a GitHub
- Usar `.env` para variables de entorno (archivo en `.gitignore`)
- Revisar commits antes de publicar
- Las credenciales se manejan directamente en n8n Credentials

---

## Variables de entorno requeridas

Crear un archivo `.env` local (nunca subir a git):

```env
# Telegram
TELEGRAM_BOT_TOKEN=tu_token_aqui

# OpenRouter
OPENROUTER_API_KEY=tu_api_key_aqui

# Google Sheets
GOOGLE_CLIENT_ID=tu_client_id.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=tu_client_secret

# Google Sheets ID
SHEETS_CATALOG_ID=id_del_spreadsheet
SHEETS_CATALOG_NAME=nombre_de_la_hoja
```

---

## Reglas críticas de infraestructura

```
⛔ NUNCA hacer:
   docker rm n8n-n8n-1
   docker volume rm n8n_n8n_data
   docker-compose down -v  (en la carpeta ~/n8n/)

✅ Para reiniciar n8n de forma segura:
   cd ~/n8n && docker-compose restart
```

---

## Acceso a servidores

- Acceso via SSH + Tailscale (sin exponer puertos públicos)
- Las llaves SSH se guardan localmente, nunca en el repositorio
- El usuario principal de Aztlan es `endor` (tiene sudo y docker)

---

## .gitignore recomendado

```
.env
*.pem
*.json
!package.json
node_modules/
__pycache__/
*.log
```
