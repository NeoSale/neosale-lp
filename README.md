# 🌐 NeoSale LP

Landing page legada em HTML estático servida via Nginx.

**Status:** Legada | **Stack:** HTML5 + CSS + JavaScript puro | **Serve:** Nginx

## 📁 Estrutura

```
neosale-lp/
├── index.html              # Homepage
├── css/                    # Estilos
├── js/                     # Scripts
├── assets/                 # Imagens e mídia
└── Dockerfile              # Container config
```

## 🚀 Desenvolvimento Local

Usar servidor HTTP local:

```bash
# Python 3
python -m http.server 8000

# Node.js (http-server)
npx http-server
```

Acesse `http://localhost:8000` ou `http://localhost:8080`

## 🚢 Deployment

Servidor web: **Nginx**

```nginx
server {
    listen 80;
    server_name lp.neosale.io;
    root /usr/share/nginx/html;
    index index.html;

    # Fallback para index.html (SPA)
    try_files $uri /index.html;
}
```

## 📝 Nota

Esta é uma landing page **legada**. Para novos projetos, usar:
- **neosale-site** (Next.js moderno)
- **neosale-lp-maya** (Vite interativo com IA)

---

**Mantido por:** Equipe NeoSale
**Status:** Em manutenção apenas
