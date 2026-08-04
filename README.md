# TikTok Live Overlay Pack — @pedropaiva63

Overlay pack para **TikTok Live Studio** (vertical 9:16) com Ome.tv + webcam + alerts.

## Estrutura

```
tiktok-overlay/
├── tiktok-ome-blur.html    # Overlay principal (Ome.tv + tua cam + blur RGPD)
├── tiktok-alerts.html      # Alerts follow/gift/sub/like
├── config.json             # Configuração central (cores, posições, SFX)
├── README.md               # Este ficheiro
└── assets/
    └── sfx/                # (opcional) SFX locais se não usares CDN
```

## Deploy no GitHub Pages (HTTPS obrigatório)

### Opção A: Git CLI (recomendado)
```bash
cd tiktok-overlay
git init
git add .
git commit -m "Initial overlay pack"
git branch -M main
git remote add origin https://github.com/xsonicxpt1/tiktok-overlay.git
git push -u origin main
```

### Opção B: Web UI (sem Git)
1. Vai a `https://github.com/xsonicxpt1/tiktok-overlay` (cria repo se não existir)
2. `Add file` → `Upload files` → arrasta **todos os ficheiros** da pasta
3. `Commit changes`

### Ativa GitHub Pages
1. No repo: **Settings** → **Pages** (menu lateral)
2. **Source**: `Deploy from branch`
3. **Branch**: `main` / `/ (root)`
4. **Save**
5. Aguarda 2–5 min → URL ativa:
   - `https://xsonicxpt1.github.io/tiktok-overlay/tiktok-ome-blur.html`
   - `https://xsonicxpt1.github.io/tiktok-overlay/tiktok-alerts.html`

---

## Configurar no TikTok Live Studio

### 1. Overlay Principal (Ome.tv + Cam)
- **Adicionar fonte** → **Navegador (Browser Source)**
- **URL**: `https://xsonicxpt1.github.io/tiktok-overlay/tiktok-ome-blur.html`
- **Largura**: `1080` | **Altura**: `1920` | **FPS**: `30`
- **CSS personalizado** (opcional, se quiseres esconder scrollbars):
  ```css
  body { overflow: hidden; }
  iframe { pointer-events: none; }
  ```
- **Permitir**: ✅ Áudio (se quiseres som do Ome.tv) | ✅ Vídeo

### 2. Alerts (Follow/Gift/Sub/Like)
- **Adicionar fonte** → **Navegador (Browser Source)**
- **URL**: `https://xsonicxpt1.github.io/tiktok-overlay/tiktok-alerts.html`
- **Largura**: `1080` | **Altura**: `1920` | **FPS**: `30`
- **CSS personalizado**:
  ```css
  body { pointer-events: none; }
  ```

### 3. Testar Alerts
Abre no browser com query strings:
- Follow: `.../tiktok-alerts.html?event=follow&user=joao123`
- Gift: `.../tiktok-alerts.html?event=gift&user=maria&gift=Rosa`
- Sub: `.../tiktok-alerts.html?event=subscribe&user=pedro`
- Like: `.../tiktok-alerts.html?event=like&user=ana`

Ou via `postMessage` (para bots/connectors):
```js
alertSource.contentWindow.postMessage({
  type: 'TIKTOK_ALERT',
  event: 'follow',
  user: 'joao123'
}, '*');
```

---

## Personalização (config.json)

Edita `config.json` e faz `git push` — atualiza em ~1 min.

### Cores/Tema
```json
"theme": {
  "primary": "#FF0050",      // TikTok pink
  "secondary": "#FFFFFF",
  "background": "#000000",
  "accent": "#00F2EA",       // Cyan accent
  "blurRadius": "8px",       // Blur no estranho (RGPD)
  "fontFamily": "system-ui, -apple-system, sans-serif"
}
```

### Posição da Webcam
```json
"webcam": {
  "x": 680, "y": 1240,       // Canto inf. dir.
  "width": 360, "height": 640,
  "borderRadius": 16,
  "borderWidth": 3,
  "borderColor": "#FF0050"
}
```

### Alerts
```json
"alerts": {
  "enabled": true,
  "sound": true,
  "sfx": { "follow": "...", "gift": "...", "subscribe": "...", "like": "..." },
  "duration": 4000,
  "position": "top-center",
  "animation": "slide-down"
}
```

### SFX Próprios
1. Põe `.mp3` em `assets/sfx/`
2. Atualiza `config.json` → `alerts.sfx` com caminhos relativos:
   ```json
   "follow": "assets/sfx/meu-follow.mp3"
   ```
3. Commit + push.

---

## Requisitos Técnicos

| Item | Obrigatório? | Notas |
|------|--------------|-------|
| **HTTPS** | ✅ Sim | TikTok Live Studio bloqueia HTTP/localhost sem certificado |
| **GitHub Pages / Netlify / Vercel** | ✅ Sim | Qualquer hosting estático HTTPS |
| **Permissão de câmara** | ✅ Sim | Browser pede na primeira carga do `tiktok-ome-blur.html` |
| **Ome.tv em iframe** | ⚠️ Parcial | Ome.tv pode bloquear `X-Frame-Options` — se não carregar, usa `scrcpy` + telemóvel |

### Se Ome.tv não carregar no iframe
Ome.tv envia `X-Frame-Options: SAMEORIGIN`. Soluções:
1. **scrcpy** (recomendado): espelha telemóvel no PC → captura janela `scrcpy` no TikTok Live Studio
2. **Helium/Chrome CDP**: já tens — usa extensão "Ignore X-Frame-Options" ou proxy local
3. **Cloudflare Workers proxy**: reescreve headers (avançado)

---

## Troubleshooting

| Problema | Solução |
|----------|---------|
| Overlay não aparece | Verifica se URL é HTTPS, aguarda propagação DNS (5 min), limpa cache do TikTok Live Studio |
| Webcam não inicia | Permite câmara no browser → recarrega overlay | 
| Blur não aplica | Verifica `config.json` → `theme.blurRadius` |
| Alerts não tocam som | Browser bloqueia autoplay — interage com a página primeiro (clique) |
| Ome.tv branco/erro | Ver `X-Frame-Options` acima — usa scrcpy |

---

## Créditos / Licença

- Código: MIT — usa, modifica, partilha
- SFX: URLs `jsdelivr.net` apontam para assets royalty-free (Pixabay, Mixkit)
- Fontes: System UI (nativa, sem download)

---

## Próximos Passos (opcional)

1. **Chat TikTok** → ativa `chat.enabled: true` no `config.json` + põe `webSocketUrl` do `tiktok-live-connector`
2. **Logo** → põe `logo.png` em `assets/` + adiciona `<img>` no HTML
3. **Cenas** → cria `starting.html`, `brb.html`, `ending.html` no mesmo padrão
4. **OBS/Streamlabs** → mesmo pack funciona (Browser Source 1080×1920)