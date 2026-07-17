# Suba — Daily Assistant 🚀

Assistente pessoal de produtividade "gamificada", inspirado no conceito de *level up* (referência ao "Arise" de Solo Leveling). O agente acompanha o usuário durante o dia via Telegram: define metas pela manhã, conversa ao longo do dia, e fecha com uma revisão/score à noite.

## Status atual

🟡 **Em desenvolvimento — Fase 2**

- [x] Fase 1 — Bot básico (Telegram Trigger + Send Message funcionando)
- [ ] Fase 2 — IA (AI Agent + Gemini)
- [ ] Fase 3 — Rotina (Schedule Trigger 09h/20h)
- [ ] Fase 4 — Memória (PostgreSQL)
- [ ] Fase 5 — Ferramentas (Google Calendar, GitHub, Strava, Notion, Gmail)

## Visão do produto

**Fluxo principal:**

- **09:00 — Planejamento do dia**: o agente pergunta as metas do dia e salva.
- **Durante o dia**: conversa livre via Telegram; o agente entende contexto, atualiza tarefas e registra informações.
- **20:00 — Fechamento do dia**: revisão com metas concluídas/pendentes, análise, score de 0-100, pontos positivos e pontos de melhoria.

## Stack

| Camada | Ferramenta |
|---|---|
| Orquestração | [n8n](https://n8n.io) (self-hosted via Docker) |
| Interface | Telegram Bot |
| Modelo de IA | Gemini API (gratuita, inicialmente) |
| Banco de dados | PostgreSQL (planejado — Fase 4) |
| Hospedagem futura | Docker + VPS |

## Arquitetura (atual)
Telegram
|
↓
n8n (Telegram Trigger)
|
↓
n8n (Send Message)

## Arquitetura (planejada)
Telegram
|
↓
n8n
|
├── Gemini API (AI Agent)
|
├── PostgreSQL (memória)
|
└── Tools/APIs (Calendar, GitHub, Strava, Notion, Gmail)

## Estrutura do repositório
jarvis-project/
├── .gitignore
├── docker-compose.yml       # configuração do ambiente n8n (Docker)
└── workflows/
└── Suba BOT.json        # export do workflow do n8n

## Rodando localmente

Pré-requisitos: Docker instalado.

```bash
docker-compose up -d
```

O n8n fica acessível em `http://localhost:5678`.

> **Nota sobre webhooks**: como o ambiente roda localmente, é necessário um túnel HTTPS público (ex: [ngrok](https://ngrok.com)) para o Telegram conseguir entregar mensagens via webhook. Rode `ngrok http 5678` e atualize as variáveis `N8N_HOST`, `N8N_PROTOCOL` e `WEBHOOK_URL` no `docker-compose.yml` com a URL gerada.

### Importando o workflow

1. Acesse `http://localhost:5678`
2. Importe o arquivo `workflows/Suba BOT.json`
3. Configure a credential do Telegram Bot (token via [@BotFather](https://t.me/BotFather)) — **não incluído no repositório por segurança**

## Segurança

- O token do bot e outras credenciais **não** são versionados — ficam armazenados de forma criptografada no volume Docker do n8n (`n8n_data`), fora do controle de versão.
- O arquivo `.env` (quando criado) está no `.gitignore`.

## Aprendizado

Este é um projeto de aprendizado prático de n8n e automação com IA. Documentação e decisões técnicas vão sendo registradas incrementalmente conforme o projeto avança pelas fases.
