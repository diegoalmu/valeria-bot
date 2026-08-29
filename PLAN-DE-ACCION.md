# 📅 Plan de Acción — Valeria Bot / Zummat Hub

## Estado actual (Agosto 2026)

### ✅ Completado
- Infraestructura Aztlan operativa (Oracle Cloud x86_64)
- n8n v2.20.11 corriendo 3+ semanas sin interrupciones
- Catálogo de 408 productos TecnoLED importado a Google Sheets
- OAuth2 Google configurado en n8n
- Integración OpenRouter + DeepSeek v3.1 funcional
- Valeria V1.3.2 respondiendo en Telegram con productos reales y precios en MXN
- Branding de n8n eliminado de mensajes Telegram
- Red Tailscale conectando todos los servidores
- ServerEndor expandido: 88GB sistema + 99GB fast_storage
- cloudflared instalado y autenticado en Aztlan

---

## 🔴 Inmediato (esta sesión)

- [ ] Configurar Cloudflare Tunnel como servicio systemd
- [ ] Verificar `n8n.tu-dominio.example` carga correctamente
- [ ] Reconstruir workflow "Zummat Bot Principal" en n8n
- [ ] Crear bot @ValeriaZummatBot en Telegram via @BotFather
- [ ] Arreglar Chat ID dinámico (bot responde a cualquier cliente)

---

## 🟡 Próxima semana

- [ ] Redis → memoria de conversación por cliente (contexto entre mensajes)
- [ ] Captura de datos: nombre, teléfono, email → Google Sheets
- [ ] Handoff humano → detectar "asesor", "humano", "hablar con alguien"
- [ ] Activar workflow en modo producción 24/7
- [ ] Configurar SSH con llave pública desde HPGris (trabajo)

---

## 🟢 Fase 2 — Zummat Hub

- [ ] PostgreSQL en ServerEndor (`/mnt/fast_storage`)
- [ ] Endpoints n8n como API REST
- [ ] Frontend panel web (React o HTML/CSS/JS)
- [ ] Subdominio `panel.zummat.com` via Cloudflare
- [ ] CRM clientes (4 tipos: Comercializadora, Referido TecnoLED, Tienda Online, Directo)
- [ ] Flujo de ventas con 8 estados
- [ ] Módulo vendedores con KPIs
- [ ] Reportes financieros y por categoría
- [ ] Módulo proveedores (TecnoLED, ipower, Hyundai)

---

## 🔵 Fase 3 — Producción real

- [ ] Migración de Telegram a Meta Business API (WhatsApp oficial)
- [ ] Quoter automático → genera PDF de cotización
- [ ] Notificaciones automáticas al equipo de ventas
- [ ] App móvil para vendedores
- [ ] Integración con sistema de facturación

---

## Métricas de éxito

| Métrica | Meta |
|---------|------|
| Tiempo de respuesta al cliente | < 3 segundos |
| Disponibilidad del bot | 99.5% uptime |
| Leads capturados por mes | > 50 |
| Cotizaciones generadas | > 20/mes |
