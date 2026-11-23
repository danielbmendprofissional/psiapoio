# PsiApoio

Um agente de IA que acompanha o paciente **entre as sessões de psicologia**, sob configuração e supervisão do psicólogo. Trabalho de faculdade (MVP funcional).

O agente é um **companheiro de escopo fechado, não um terapeuta**: reforça tarefas combinadas, tira dúvida de como executar um exercício, registra o humor e acolhe de forma breve. O psicólogo lê a transcrição completa, é avisado por **Alerta** quando uma mensagem pede atenção ou indica risco, e chega à sessão com **Resumo diário** e **Pauta**.

## Repositórios

| Repositório | O que é | Tecnologia |
|---|---|---|
| **[psiapoio-workspace](https://github.com/danielbmendprofissional/psiapoio-workspace)** | Specs, decisões (ADRs), glossário, modelo de dados e roteiro de demonstração | Markdown |
| **[psiapoio-api](https://github.com/danielbmendprofissional/psiapoio-api)** | Autenticação, pacientes, conversa, alertas, resumo e pauta; dona do banco | .NET 10, PostgreSQL |
| **[psiapoio-frontend](https://github.com/danielbmendprofissional/psiapoio-frontend)** | Painel do psicólogo e conversa do paciente | Angular 22, spartan/ui, Tailwind |
| **[psiapoio-agent](https://github.com/danielbmendprofissional/psiapoio-agent)** | O agente: escopo fechado, classificação de risco, memória, resumo e pauta | Python, FastAPI, LLM por interface |

Cada repositório tem README próprio com instruções de execução e testes. Para subir tudo junto e gravar uma demonstração, siga o [COMO-RODAR](https://github.com/danielbmendprofissional/psiapoio-workspace/blob/main/COMO-RODAR.md) do workspace.

## Como funciona

```
Angular --HTTP--> API .NET --SQL--> PostgreSQL
                     |
                     +--HTTP + chave--> Serviço Python --> LLM
```

- A **API é a única dona dos dados** e do controle de acesso; o serviço Python é sem estado.
- **Risco:** cada mensagem do paciente é classificada em `nenhum`, `atenção` ou `risco`. Em `risco` a resposta é **fixa, não gerada pelo LLM**, e o psicólogo recebe um Alerta no painel e um e-mail sem conteúdo clínico. Mensagem sem classificação nunca é tratada como "sem risco".
- **Consentimento:** o paciente sabe, antes da primeira mensagem, que o psicólogo lê toda a conversa.
- O agente só responde depois que o psicólogo **aprova o Perfil do agente**.

## Decisões de projeto

As decisões estão registradas como ADRs no [workspace](https://github.com/danielbmendprofissional/psiapoio-workspace/tree/main/docs/adr): escopo fechado do agente, reação a risco, API dona do Postgres, LLM atrás de interface de provedor, direção de design e autenticação por JWT em cookie httpOnly.

## Limitações

A detecção de risco por LLM não é infalível (medida com 32 mensagens fictícias rotuladas; recall de risco 22/22 na última avaliação); não há encaminhamento a serviço de emergência; a sessão dura 60 minutos. **Só dados fictícios**: não use com pacientes reais.
