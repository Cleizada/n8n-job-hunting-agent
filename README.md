# n8n Job Hunting Agent

Agente que **busca vagas remotas automaticamente, pontua a aderência com IA e envia só as melhores pelo Telegram**, sem precisar ficar abrindo site de vagas todo dia.

## Como funciona

```
Agendamento (3x/dia)
      │
      ▼
Apify – Indeed Scraper ──► coleta as vagas
      │
      ▼
Claude Haiku (scorer) ──► nota de 0–100 de aderência ao meu perfil
      │
      ▼
Filtro IF: nota mínima E remoto = true
      │
      ▼
Tabela vagas_status ──► registra a vaga (status "Não visualizada")
      │
      ▼
Telegram ──► alerta com botões ✅ Aplicar / ❌ Descartar
```

## Stack

- **n8n** (self-hosted via npm) com **ngrok** para expor webhooks
- **Apify** (Indeed Scraper)
- **Claude Haiku** via API para pontuar cada vaga
- **Telegram Bot** para os alertas
- **n8n Data Table** (`vagas_status`) para registrar o status de cada candidatura

## Decisões de projeto

- **Haiku como scorer:** modelo rápido e barato, suficiente para classificar dezenas de vagas por rodada.
- **Filtro antes da notificação:** só chega no Telegram o que passa da nota mínima (configurável no nó `If`) e é remoto, o que evita ruído.
- **Resposta em JSON:** o Claude devolve `match_score`, `is_remote`, `reason` e uma `suggested_response` (resposta de triagem em inglês), e um nó de código faz o parse.
- **Execução 3x ao dia:** equilibra vagas novas com custo de scraping e de API.

## Como rodar

1. Instale o n8n: `npm install -g n8n` e rode `n8n start`.
2. Importe o workflow: **Workflows → Import from file →** `n8n-job-hunting-agent-workflow.json`.
3. Configure as credenciais no n8n (nenhuma chave vem no arquivo):
   - Apify API token (na URL do nó *Run Apify Scraper*)
   - Anthropic API key (cabeçalho `x-api-key` do nó *AI Job Match Scorer*)
   - Telegram Bot token + chat ID
4. Ajuste o prompt do scorer com o seu perfil e a palavra-chave de busca.
5. Ative o workflow.

## Estrutura

```
├── n8n-job-hunting-agent-workflow.json   # workflow exportado do n8n (sem credenciais)
├── README.md
└── docs/
    └── fluxo.png   # print do workflow no n8n
```

## Próximos passos

- [ ] Tratar os botões ✅/❌ do Telegram (hoje só enviam o callback) para atualizar o status na tabela
- [ ] Evitar vagas duplicadas entre rodadas
- [ ] Adicionar mais fontes além do Indeed
- [ ] Resumo semanal das vagas por Telegram

## Autor

Cleidson Soares — [LinkedIn](https://www.linkedin.com/in/cleidson-soares)
