# 🏗️ Arquitectura del Sistema — Valeria Bot / Zummat

## Servidores

| Servidor | Hardware | OS | IP Tailscale | Rol |
|----------|----------|-----|--------------|-----|
| **Aztlan** | Oracle Cloud x86_64, 2 CPU, 954MB RAM | Ubuntu Server | 100.X.X.1 | n8n + Valeria (producción) |
| **ServerEndor** | AMD Ryzen 3 3200G, 4 núcleos, 13GB RAM | Ubuntu Server 24/7 | 100.X.X.2 | Zummat Hub + PostgreSQL (fase 2) |
| **EndorPC** | Intel i5-8400T, 6 núcleos, 31GB RAM, 234GB NVMe | Linux Mint | 100.X.X.3 | Desarrollo y pruebas |

## Red Tailscale

Todos los servidores están conectados mediante Tailscale (VPN mesh). Permite SSH directo entre máquinas sin exponer puertos públicos ni requerir IP fija.

```
Aztlan        100.X.X.1
EndorPC       100.X.X.3
ServerEndor   100.X.X.2
Fortuna       100.X.X.4
HPGris/Trabajo 100.X.X.5
```

## Contenedor n8n en Aztlan

```
Contenedor:  n8n-n8n-1
Imagen:      docker.n8n.io/n8nio/n8n
Volumen:     n8n_n8n_data → /home/node/.n8n
Red Docker:  n8n_default
Puerto:      0.0.0.0:5678
Uptime:      3+ semanas continuo
```

> ⚠️ NUNCA recrear `n8n-n8n-1` ni eliminar `n8n_n8n_data`

## Acceso público

```
Cliente → https://n8n.tu-dominio.example → Cloudflare Tunnel → localhost:5678 → n8n
```

Cloudflare Tunnel instalado en Aztlan como servicio, sin necesidad de IP pública fija ni apertura de puertos en el router.

## Disco ServerEndor

```
/dev/sda  112GB → sistema Ubuntu → /           (88GB libres)
/dev/sdb  112GB → datos          → /mnt/fast_storage  (99GB libres)
```

El segundo disco `/mnt/fast_storage` está reservado para PostgreSQL cuando se construya el Zummat Hub.

## Decisiones de diseño

**n8n como orquestador** — flujo de ventas determinista, integraciones nativas, menor costo de tokens vs agentes autónomos.

**DeepSeek v3.1** — calidad comparable a GPT-4 en español con costo 10-20x menor.

**Cloudflare Tunnel** — funciona con IP dinámica, sin costo, sin puertos expuestos.

**Separación de roles** — Aztlan solo corre el bot (recursos limitados). ServerEndor corre datos y panel (diseñado para 24/7).
