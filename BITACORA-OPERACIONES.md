# 📓 Bitácora de Operaciones — Valeria Bot / Zummat

---

## 2026-08-29 — Auditoría de infraestructura y recuperación

### 🧠 Estado del arquitecto
- **Enfoque:** Recuperar el estado del sistema tras 2 semanas de pausa. Localizar contenedores, túneles y configuración de n8n.
- **Energía:** Alta, sesión larga y productiva.

### 🚀 Logros técnicos

**Auditoría de Aztlan:**
- Confirmado que el contenedor real de n8n es `n8n-n8n-1`, volumen `n8n_n8n_data`, red `n8n_default`
- Descubierto que existían DOS usuarios en Aztlan: `endor` (dueño real con sudo/docker-compose) y `ubuntu` (solo acceso docker)
- El docker-compose original está en `~/n8n/docker-compose.yml` del usuario `endor`
- n8n lleva **3+ semanas corriendo** sin interrupciones — todos los datos intactos en `n8n_n8n_data`
- Confirmado que `n8n.tu-dominio.example` carga (sin workflow todavía)

**Cloudflared:**
- Detectado que el binario anterior era ARM64 (error de arquitectura — Aztlan es x86_64)
- Instalado `cloudflared-linux-amd64 v2026.8.2` correctamente
- Autenticado con Cloudflare — cert en `/home/endor/.cloudflared/cert.pem`
- Confirmado que ya existe un túnel creado previamente (nombre: `zummat-tunnel`)
- Pendiente: crear `config.yml` y activar como servicio systemd

**Tailscale:**
- Mapa completo de la red confirmado: Aztlan, EndorPC, ServerEndor, Fortuna, HPGris conectados
- Acceso SSH desde trabajo via Tailscale funcional con usuario correcto (`endor` en Aztlan)

**ServerEndor:**
- Ejecutado `lvextend + resize2fs` — disco principal expandido de 54GB a **107GB** (88GB libres)
- Segundo disco `/mnt/fast_storage` confirmado con **99GB libres** — reservado para PostgreSQL
- Hardware confirmado: AMD Ryzen 3 3200G, 4 núcleos, 13GB RAM, Ubuntu Server 24/7

**Documentación:**
- Generada bitácora completa del proyecto para portafolio GitHub
- Creada estructura completa del repositorio `zummat-valeria-bot`

### 📝 Notas libres
- Los workflows de Valeria se perdieron cuando Oracle revocó la máquina original de alta gama (4 OCPU, 24GB RAM). La nueva instancia es x86_64 con 2 CPU y 954MB RAM.
- Aztlan está al límite de RAM con n8n corriendo — solo alcanza para el bot. El Zummat Hub irá en ServerEndor.
- El túnel de Cloudflare ya existía pero sin config.yml ni servicio systemd activo.

### ⏭️ Próximo paso
Verificar que Valeria responde a cualquier cliente (no solo a Diego), captura de datos, handoff humano.

---

## 2026-08-29 — Valeria V2 funcionando con bot dedicado

### 🧠 Estado del arquitecto
- **Enfoque:** Reconstruir workflow completo desde cero y resolver bug crítico del HTTP Request en n8n 2.31.5.
- **Energía:** Alta, sesión muy larga pero con resultado exitoso.

### 🚀 Logros técnicos

**Cloudflare Tunnel:**
- Tunnel `zummat-tunnel` configurado (ID: `c5044d81-179a-4ea1-b356-be363543353b`)
- Config en `/etc/cloudflared/config.yml` apuntando a `localhost:5678`
- Instalado como servicio systemd — sobrevive reinicios
- `n8n.tu-dominio.example` accesible 24/7 ✅

**Bot Telegram:**
- Creado bot dedicado @ValeriaZummatBot (token nuevo)
- Desvinculado del bot personal @demianaux_bot de Diego

**Workflow reconstruido:**
- Telegram Trigger → Get row(s) in sheet → Code JS → HTTP Request → Code JS1 → Send message
- Google Sheets credencial OAuth2 reconectada
- OpenRouter + DeepSeek v3.1 funcionando
- 408 productos TecnoLED consultados en tiempo real

**Bug crítico resuelto — n8n 2.31.5 HTTP Request:**
- Problema: el `=` al inicio de expresiones (`={{ }}`) convierte objetos a string en campos JSON
- Solución: usar `{{ }}` sin el `=` en campos JSON del HTTP Request y Telegram
- Aplica a: Body Content Type JSON → Using JSON, y campos Chat ID / Text del nodo Telegram

**Chat ID dinámico:**
- Valeria responde al cliente que escribe, no solo a Diego
- Campos Chat ID y Text sin el `=` → `{{ $json.chatId }}` y `{{ $json.texto }}`

**Primera respuesta de Valeria (bot nuevo):**
> "¡Hola! Soy Valeria, asesora de ventas de Zummat. Para poder recomendarte los mejores productos de iluminación LED, ¿podrías contarme sobre el espacio que necesitas iluminar?"

### 📝 Notas libres
Sesión muy larga con múltiples obstáculos técnicos. El bug del HTTP Request en n8n 2.31.5 consumió la mayor parte del tiempo — la clave fue encontrar en la comunidad que `={{ }}` convierte a string mientras `{{ }}` pasa el objeto directamente. Una vez resuelto, todo funcionó en cadena.

### ⏭️ Próximos pasos
1. Confirmar que Valeria responde a cualquier cliente público
2. Activar workflow en modo producción 24/7
3. Captura de datos del cliente (nombre, teléfono, email)
4. Handoff humano (keywords: "asesor", "humano", "hablar con alguien")

---

## 2026-06-26 — Construcción de Valeria V1.3.2

### 🧠 Estado del arquitecto
- **Enfoque:** Construir el bot de ventas Valeria desde cero en una sesión.
- **Energía:** Alta, sesión intensiva de 3+ horas.

### 🚀 Logros técnicos

**Catálogo de productos:**
- Procesada lista de precios TecnoLED Junio 2026 (PDF distribuidora)
- Importados **408 productos** en Google Sheets con estructura normalizada
- Precios calculados: `precio_venta = precio_costo × 1.30` (margen 30% Zummat)
- Categorías: Campanas Industriales, Reflectores, Poste/Vialidad, Tower Lights, Wall Pack, Paneles, Downlights, Luminario Oficina, Riel Magnético, Tubos T8/T5, Bulbos, Solar, Tiras LED, Ventiladores, Fuentes de Poder, Exteriores Decorativos

**Google Sheets + n8n:**
- Creado proyecto "Zummat Bot" en Google Cloud Console
- Activadas APIs: Google Sheets y Google Drive
- Generadas credenciales OAuth2 con URI de callback correcto
- Credencial conectada exitosamente en n8n

**Integración OpenRouter + DeepSeek:**
- Configurado nodo HTTP Request en n8n con Bearer token
- Modelo: `deepseek/deepseek-chat-v3.1` (NO usar `openrouter/free` — límite diario)
- System prompt diseñado con catálogo completo incrustado y reglas de comportamiento

**Workflow funcional:**
```
Telegram Trigger → Get rows Google Sheets → Code JS (build prompt)
→ HTTP Request OpenRouter → Code JS (parse + chatId) → Send Telegram
```

**Valeria respondiendo:**
- Bot selecciona exactamente 3 productos del catálogo real
- Presenta nombre, descripción y precio en MXN
- Hace preguntas inteligentes de seguimiento
- Se presenta como "Valeria, asesora de ventas de Zummat"
- Branding de n8n eliminado (Additional Fields → Append n8n Attribution → OFF)

### 📝 Notas libres
- El primer mensaje de Valeria fue: *"¡Hola! Bienvenido a Zummat, tu solución en iluminación LED. Soy Valeria, tu asesora."* — fue un momento de mucha satisfacción ver el sistema funcionando.
- Problema identificado: Chat ID hardcodeado — bot solo responde al Chat ID de Diego, no a otros clientes. Fix pendiente.

### ⏭️ Próximo paso
Activar workflow en modo producción 24/7, arreglar Chat ID dinámico, implementar captura de datos del cliente.

---

## Registro de problemas críticos

| Fecha | Problema | Causa | Solución |
|-------|----------|-------|----------|
| 2026-08 | Dos contenedores n8n en conflicto | Dos usuarios en Aztlan levantaron docker-compose independientes | Usar solo `n8n-n8n-1`, nunca recrear |
| 2026-08 | Cloudflare 502 | Redes Docker separadas | Tunnel apunta a `localhost:5678` |
| 2026-08 | Oracle quitó máquina original | Recursos ARM revocados | Reconstruir en x86_64 nuevo |
| 2026-08 | Chat ID hardcodeado | Campo no aceptaba expresión | Pasar chatId como Number desde Code JS |
| 2026-08 | Binario cloudflared incorrecto | Asumimos ARM, es x86_64 | Descargar `cloudflared-linux-amd64` |
| 2026-08 | Disco ServerEndor solo 54GB | LVM no expande automático | `lvextend + resize2fs` → 107GB |
