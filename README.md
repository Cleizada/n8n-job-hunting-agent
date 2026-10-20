# Agente de vagas com n8n

Eu estava procurando emprego e passando um tempo enorme abrindo site de vaga, lendo descrição e descobrindo que metade nem era remota. Montei este fluxo no n8n para fazer essa triagem por mim.

## Como funciona

Três vezes por dia o fluxo:

1. busca vagas de "suporte remoto" no Indeed usando um scraper da Apify (até 20 por rodada);
2. manda cada vaga para o Claude Haiku junto com um resumo do meu perfil, e ele devolve uma nota de 0 a 100, se a vaga é remota, o motivo da nota e uma resposta curta de triagem em inglês;
3. descarta o que não é remoto ou não passa da nota mínima;
4. grava a vaga numa tabela do próprio n8n (`vagas_status`) e me manda no Telegram com o link.

A nota mínima fica no nó `If`. Hoje está em 0, e a ideia é subir para uns 60 quando eu quiser menos ruído.

## O que eu usei

n8n rodando local, Apify (Indeed Scraper), API da Anthropic com o Claude Haiku, bot do Telegram e uma Data Table do n8n.

Escolhi o Haiku porque é rápido e barato, e para classificar vaga não precisa de mais do que isso.

## Como rodar

1. Instale o n8n (`npm install -g n8n`) e abra com `n8n start`.
2. Importe o arquivo `n8n-job-hunting-agent-workflow.json`.
3. Coloque as suas credenciais. O arquivo não traz nenhuma:
   - token da Apify, na URL do nó "Run Apify Scraper";
   - chave da Anthropic, no cabeçalho `x-api-key` do nó "AI Job Match Scorer";
   - token do bot do Telegram e o seu chat ID.
4. Troque o perfil e a busca no prompt do scorer pelos seus.

## O que ainda não está bom

- Os botões "Aplicar" e "Descartar" do Telegram aparecem, mas ainda não fazem nada. Falta tratar o retorno deles para atualizar a tabela.
- A mesma vaga pode aparecer de novo em rodadas diferentes, porque ainda não checo duplicadas.
- Às vezes a conexão com o Telegram dá timeout, e eu ainda preciso configurar tentativas automáticas.

## Aviso

O n8n exporta o workflow com as chaves que estiverem escritas dentro dos nós. Se você exportar o seu, procure pelas chaves no arquivo antes de publicar.
