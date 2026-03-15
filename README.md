# dB Meter — Telegram Mini App

Medidor profissional de nível sonoro como Telegram Mini App.  
Stack: Python (bot) + Vanilla HTML/CSS/JS (Mini App).

---

## Arquitetura

```
Telegram App
    │
    ├── Bot (bot.py)          ← roda local ou em VPS (Python)
    │      │ /start
    │      └── botão → abre WebView
    │
    └── Mini App (index.html) ← hospedado em HTTPS (GitHub Pages, Vercel, etc.)
           │
           └── Web Audio API → microfone → DSP → canvas
```

---

## Pré-requisitos

| Item | Detalhe |
|---|---|
| Python | 3.10+ |
| Token do bot | Criado no @BotFather |
| Hospedagem HTTPS | `getUserMedia` exige HTTPS |

---

## Passo 1 — Hospedar o index.html

### Opção A: GitHub Pages (gratuito, recomendado)

```bash
# Criar repositório público no GitHub, ex.: github.com/seunome/dbmeter
git init
git add index.html
git commit -m "dB Meter Mini App"
git remote add origin https://github.com/SEU_USUARIO/dbmeter.git
git push -u origin main
```

Ativar Pages em: Settings → Pages → Branch: main → Save.  
URL gerada: `https://SEU_USUARIO.github.io/dbmeter/`

### Opção B: Vercel

```bash
npm i -g vercel
vercel --name dbmeter
```

---

## Passo 2 — Configurar o bot.py

Edite as duas constantes no topo do arquivo:

```python
BOT_TOKEN    = "123456789:ABCdef..."          # token do @BotFather
MINI_APP_URL = "https://seunome.github.io/dbmeter/"
```

---

## Passo 3 — Registrar a URL no @BotFather

No Telegram, abra @BotFather:

```
/mybots → Seu bot → Bot Settings → Menu Button → Edit Menu Button URL
→ cole a mesma MINI_APP_URL
```

---

## Passo 4 — Instalar dependências e rodar

```bash
pip install -r requirements.txt
python bot.py
```

O bot fica em polling. Para rodar em background no servidor:

```bash
nohup python bot.py &
# ou com systemd/screen/tmux
```

---

## Calibração

O app é relativo por padrão (REF_LEVEL = 90 dB interno).  
Para calibrar:

1. Use uma fonte de referência conhecida, ex.: 94 dB SPL @ 1 kHz  
   (calibrador de microfone ou app de referência confiável)
2. Abra o dB Meter → Settings → Calibração
3. Ajuste o slider até o display mostrar 94.0 dB
4. Pronto — as medições ficarão absolutas para esse dispositivo

### Curva do Microfone

Microfones de smartphone/smartwatch têm resposta irregular (~100 Hz–8 kHz).  
Se você tiver dados de resposta do mic (dBFS/Pa por frequência), insira os  
desvios em cada banda de oitava em Settings → Curva do Microfone.

---

## Funcionalidades implementadas

| Feature | Detalhe |
|---|---|
| Meter | Nível instantâneo, barra colorida (verde/amarelo/vermelho), MIN/AVG/MAX |
| Spectrum | 8 bandas de oitava (125 Hz–16 kHz), peak hold com decay |
| History | Gráfico rolling 30 s, resetável por long-press |
| Ponderação | A / C / Z — fórmula analítica IEC 61672 (idêntica ao app Wear OS) |
| Calibração | Offset global ±20 dB |
| Mic Curve | Correção por banda ±20 dB |
| Tema | M3 Expressive dark, respeita colorScheme do Telegram |

---

## Limitações

- `getUserMedia` **exige HTTPS** — não funciona em `http://` nem `file://`
- Telegram iOS abre o Mini App no Safari WebView; pedir permissão de microfone  
  pode exigir que o usuário vá em Configurações → Telegram → Microfone
- A latência do Web Audio API varia por dispositivo (tipicamente 50–100 ms)
- Não há sincronização com o app Wear OS — são instâncias independentes
