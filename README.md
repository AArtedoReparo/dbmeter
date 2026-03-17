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
