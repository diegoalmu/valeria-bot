# 🤖 Valeria — Bot de Ventas Automatizado para Zummat

> Asesora de ventas con inteligencia artificial que atiende clientes en Telegram 24/7, consulta el catálogo real de productos LED y responde como una vendedora profesional.

---

## ¿Qué es esto?

**Valeria** es un chatbot de ventas construido para **Zummat**, empresa mexicana de iluminación LED TecnoLED y plantas de emergencia (ipower / Hyundai).

El bot recibe mensajes de clientes, entiende su necesidad y recomienda exactamente 3 productos del catálogo con nombre, descripción y precio en MXN — todo sin intervención humana.

---

## Stack tecnológico

| Componente | Tecnología |
|------------|-----------|
| Orquestador | n8n v2.20.11 (self-hosted) |
| IA | DeepSeek v3.1 via OpenRouter |
| Canal | Telegram Bot API |
| Catálogo | Google Sheets (408 productos) |
| Infraestructura | Oracle Cloud (servidor Aztlan) |
| Acceso público | Cloudflare Tunnel (HTTPS) |
| Red privada | Tailscale VPN mesh |

---

## Arquitectura del flujo

```
Cliente escribe en Telegram
         ↓
   Telegram Trigger (n8n)
         ↓
   Google Sheets → catálogo 408 productos TecnoLED
         ↓
   Code JS → construye prompt con catálogo completo
         ↓
   HTTP Request → OpenRouter → DeepSeek v3.1
         ↓
   Code JS → extrae respuesta + Chat ID dinámico
         ↓
   Telegram → responde al cliente con 3 productos
```

---

## Estado del proyecto

- ✅ Infraestructura Aztlan operativa
- ✅ n8n corriendo estable (3+ semanas)
- ✅ Catálogo 408 productos importado
- ✅ Bot respondiendo en Telegram con IA
- 🔄 Reconstrucción del workflow (workflows perdidos por falla Oracle)
- 🔄 Bot dedicado @ValeriaZummatBot (pendiente)
- 📅 Redis + PostgreSQL (próxima fase)
- 📅 Migración a WhatsApp (Meta Business API)

---

## Estructura del repositorio

```
zummat-valeria-bot/
├── README.md               ← este archivo
├── ARQUITECTURA.md         ← infraestructura completa
├── INSTALACION.md          ← guía paso a paso para reproducir
├── BITACORA-OPERACIONES.md ← registro de sesiones y decisiones
├── PLAN-DE-ACCION.md       ← roadmap y pendientes
├── SEGURIDAD.md            ← reglas de seguridad (sin credenciales)
├── workflows/
│   └── valeria-v1.json     ← workflow n8n exportado
└── catalogo/
    └── catalogo_zummat.csv ← 408 productos TecnoLED
```

---

## Autor

**Diego Alvarado** — CEO Zummat / Estudiante de Licenciatura en TIC  
GitHub: [@diegoalmu](https://github.com/diegoalmu)
