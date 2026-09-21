# Validação da minha execução

Data da validação: 21/09/2026

Este documento registra os testes que realizei após executar o projeto da
Imersão Engenharia de Dados com IA da Alura em meu próprio ambiente Databricks.

O objetivo foi comparar as respostas do Genie Agent com as consultas SQL de
referência do projeto e identificar possíveis problemas de interpretação.

## Resultado geral

| Teste | Resultado |
|---|---|
| P1 — aeroportos e rotas com maiores atrasos | ✅ Com ressalva semântica |
| P2 — evolução do atraso ao longo do dia | ✅ Validado |
| P3 — pontualidade e cancelamento por companhia | ✅ Com ressalva semântica |
| P4 — doméstico x internacional | ✅ Validado |
| P5 — recuperação de atraso em voo | ✅ Com ressalva semântica |

## Observações dos testes

### P1 — Aeroportos e rotas

Os resultados numéricos foram coerentes com as consultas SQL de referência.

Durante a validação, foi identificado que expressões como "maiores atrasos"
podem ser interpretadas de duas formas diferentes:

- frequência de atraso: percentual de voos atrasados;
- severidade do atraso: atraso médio em minutos.

Esse comportamento motivou a inclusão de uma regra explícita nas instruções
do Genie para diferenciar as duas métricas.

### P2 — Evolução do atraso ao longo do dia

A resposta reproduziu corretamente o padrão observado na consulta SQL por
hora do dia.

Durante os testes, o agente inicialmente utilizou linguagem causal para
explicar o crescimento dos atrasos. Como os dados demonstram associação
temporal, mas não causalidade, o metadado de `hora_partida_prevista` e as
instruções do agente foram revisados.
