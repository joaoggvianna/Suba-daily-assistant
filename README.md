# Suba — Daily Assistant 🚀

Assistente pessoal de produtividade "gamificada", inspirado no conceito de *level up* (referência ao "Arise" de Solo Leveling). O agente acompanha o usuário durante o dia via Telegram: define metas pela manhã, conversa ao longo do dia, e fecha com uma revisão/score à noite.

## Status atual

🟡 **Em desenvolvimento — Fase 3**

- [x] Fase 1 — Bot básico (Telegram Trigger + Send Message funcionando)
- [x] Fase 2 — IA (AI Agent + Gemini, personalidade do Suba definida via System Prompt)
- [x] Fase 3 — Rotina (Schedule Trigger 09h/20h)
- [ ] Fase 4 — Memória (PostgreSQL)
- [ ] Fase 5 — Ferramentas (Google Calendar, GitHub, Strava, Notion, Gmail)

### Notas da Fase 2

- Modelo utilizado: `gemini-flash-lite-latest` (outros modelos como `gemini-2.0-flash` e `gemini-1.5-flash` retornaram erro de cota `429` na camada gratuita)
- O System Prompt define identidade, tom de voz e comportamento do Suba nos 3 momentos do dia (planejamento matinal, acompanhamento, revisão noturna)
- ⚠️ **Sem memória persistente ainda**: cada mensagem dispara uma execução independente do workflow. O Suba pode parecer "lembrar" do contexto dentro da mesma sessão de chat, mas nenhum dado é salvo entre execuções — isso será resolvido na Fase 4.

### Notas da Fase 3

- Dois `Schedule Trigger` adicionados ao mesmo workflow principal (não em workflows separados): um às 09:00 (planejamento) e outro às 20:00 (revisão noturna), cada um conectado ao seu próprio node de Send Message.
- ⚠️ **Revisão noturna ainda simplificada**: como não há memória persistente (Fase 4), o fluxo das 20h pede que o próprio usuário resuma o dia na hora, e o AI Agent gera a análise/score a partir dessa resposta — não a partir de um histórico real das metas/relatos ao longo do dia. Isso será aprimorado quando o PostgreSQL for implementado.
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
