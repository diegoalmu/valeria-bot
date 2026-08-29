# 🛠️ Guía de Instalación — Valeria Bot

## Requisitos previos

- Servidor Ubuntu con acceso SSH
- Cuenta de Cloudflare con dominio configurado
- Cuenta de Telegram → bot creado via @BotFather
- Cuenta de Google → proyecto en Google Cloud Console
- Cuenta de OpenRouter con créditos

---

## 1. Instalar Docker

```bash
sudo apt update && sudo apt install -y docker.io docker-compose
sudo usermod -aG docker $USER
```

---

## 2. Levantar n8n

```bash
mkdir ~/n8n && cd ~/n8n
nano docker-compose.yml
```

Contenido del `docker-compose.yml`:

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_SECURE_COOKIE=false
      - N8N_HOST=n8n.tudominio.xyz
      - N8N_PROTOCOL=https
      - WEBHOOK_URL=https://n8n.tudominio.xyz/
      - N8N_EDITOR_BASE_URL=https://n8n.tudominio.xyz/
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

```bash
docker-compose up -d
docker ps  # verificar que corre
```

---

## 3. Instalar Cloudflare Tunnel

```bash
# Para x86_64
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -o cloudflared
chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/
cloudflared --version

# Autenticar
cloudflared tunnel login

# Crear túnel
cloudflared tunnel create zummat-tunnel
cloudflared tunnel list  # anotar el Tunnel ID
```

Crear `~/.cloudflared/config.yml`:

```yaml
tunnel: TU-TUNNEL-ID
credentials-file: /home/TU_USUARIO/.cloudflared/TU-TUNNEL-ID.json

ingress:
  - hostname: n8n.tudominio.xyz
    service: http://localhost:5678
  - service: http_status:404
```

```bash
cloudflared tunnel route dns zummat-tunnel n8n.tudominio.xyz

# Instalar como servicio
sudo cloudflared service install
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

---

## 4. Configurar Google Sheets

1. Ir a [console.cloud.google.com](https://console.cloud.google.com)
2. Crear proyecto "Zummat Bot"
3. Activar API: Google Sheets API + Google Drive API
4. Crear credenciales OAuth 2.0 tipo "Aplicación web"
5. URI de redirección: `https://n8n.tudominio.xyz/rest/oauth2-credential/callback`
6. Descargar JSON con Client ID y Client Secret

En n8n → Credentials → Google Sheets OAuth2 → pegar Client ID y Secret → Sign in with Google.

---

## 5. Crear bot de Telegram

1. Abrir Telegram → buscar @BotFather
2. `/newbot`
3. Nombre: `Valeria Zummat`
4. Username: `ValeriaZummatBot`
5. Copiar el token

En n8n → Credentials → Telegram → pegar token.

---

## 6. Configurar OpenRouter

1. Registrarse en [openrouter.ai](https://openrouter.ai)
2. Agregar créditos (mínimo $5 USD para pruebas)
3. Generar API Key

En n8n → nodo HTTP Request → Header Auth → `Authorization: Bearer TU_API_KEY`

---

## 7. Importar el workflow

1. En n8n → menú hamburguesa → Import from file
2. Seleccionar `workflows/valeria-v1.json`
3. Activar el workflow (toggle)
4. Probar enviando mensaje al bot de Telegram

---

## Verificar que todo funciona

```bash
# n8n corriendo
docker ps | grep n8n

# Túnel activo
sudo systemctl status cloudflared

# n8n responde
curl -s http://localhost:5678/healthz
```
