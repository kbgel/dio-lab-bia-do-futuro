# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| [historico_atendimento.csv](https://github.com/kbgel/dio-lab-bia-do-futuro/blob/main/data/historico_atendimento.csv) | CSV | Contextualizar interações anteriores |
| [perfil_investidor.json](https://github.com/kbgel/dio-lab-bia-do-futuro/blob/main/data/perfil_investidor.json) | JSON | Personalizar recomendações |
| [transacoes.csv](https://github.com/kbgel/dio-lab-bia-do-futuro/blob/main/data/transacoes.csv) | CSV | Analisar padrão de gastos do cliente |
| [cartilha_seguranca.json](https://github.com/kbgel/dio-lab-bia-do-futuro/blob/main/data/cartilha_seguranca.json) | JSON | Define as respostas preventivas e ações imediatas que a Cybele deve usar como referência para situações envolvendo os principais tipos de golpes financeiros|


---

## Adaptações nos Dados

- Remoção do arquivo `produtos_financeiros.json` da base utilizada, pois contém apenas produtos de investimento e não se aplica ao escopo de segurança digital da Cybele.
- Adição de duas entradas fictícias relacionadas ao tema de segurança na base de conhecimento `historico_atendimento.csv`.
- Criação da base de conhecimento `cartilha_seguranca.json` com os principais cenários de golpes (falso funcionário, phishing e clonagem de cartão).

---

## Estratégia de Integração

### Como os dados são carregados?

```python
import pandas as pd
import json

perfil = json.load(open('./data/perfil_investidor.json'))
transacoes = pd.read_csv('./data/transacoes.csv')
historico = pd.read_csv('./data/historico_atendimento.csv')
seguranca = json.load(open('./data/cartilha_seguranca.json'))
```

### Como os dados são usados no prompt?

Os dados são divididos em duas estratégias:
1. **System Prompt (Estático):** O conteúdo da `cartilha_seguranca` é injetado diretamente nas instruções iniciais (System Prompt) do agente. Como é um conteúdo enxuto e essencial, garante que a Cybele sempre saberá identificar e orientar sobre os golpes mapeados.
2. **Injeção de Contexto (Dinâmico):** Os dados do cliente (`perfil_investidor`,  `historico_atendimento` e `transacoes`) não vão inteiros no prompt. Quando o cliente inicia o chat, o sistema filtra apenas as informações referentes àquele cliente e as envia como "contexto" no início da conversa.

---

## Exemplo de Contexto Montado

Quando o cliente inicia a conversa, o sistema formata as informações dele assim para a Cybele ler:

```text
[Contexto do Cliente]
Perfil: Conservador, baixa familiaridade digital
Histórico de Atendimento recente:
- 11/11/2025: Cliente solicitou bloqueio do seu cartão de crédito após suspeita de ter sido clonado (resolvido).

[Diretrizes de Segurança (Cartilha)]
- Cartão Virtual: Orientar sempre o uso de cartão virtual para compras online. Ação: Se houver dúvida, pare a compra e gere o cartão virtual no app.
```
