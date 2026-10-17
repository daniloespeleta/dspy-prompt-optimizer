# Relatório Final — DSPy Prompt Optimizer

**Data:** 16/09/2026  
**DSPy:** 3.3.1  
**Ambiente:** Windows + Python 3.12 + Ollama  
**Status:** Experimento concluído

---

## 1. Objetivo

O objetivo deste experimento foi testar, de forma prática e mensurável, se um modelo local e gratuito pode melhorar significativamente seu desempenho quando o comportamento do sistema é otimizado com DSPy.

A arquitetura utilizada foi do tipo **Professor-Aluno**:

- **Aluno:** Llama 3.1 8B executado localmente via Ollama.
- **Professor:** GPT-4.1 mini, utilizado durante a otimização.
- **Framework:** DSPy 3.3.1.

O modelo local continuou responsável por realizar a tarefa final.

O modelo pago foi utilizado apenas como componente auxiliar durante os processos de otimização.

Foram comparadas três condições:

1. Baseline humano simples;
2. GEPA;
3. MIPROv2.

A hipótese experimental inicial era que o GEPA seria o principal optimizer e o MIPROv2 funcionaria como challenger.

Os resultados não confirmaram essa hipótese nesta tarefa.

---

# 2. Tarefa

A tarefa escolhida foi **classificação binária de sentimento em português brasileiro**.

O modelo precisava classificar cada frase exclusivamente como:

- `positivo`
- `negativo`

O dataset foi propositalmente construído para conter casos mais difíceis do que análise de sentimento tradicional.

Foram utilizados exemplos envolvendo:

- ironia;
- sarcasmo;
- negação;
- sentimentos mistos;
- contraste;
- mudança de sentimento ao longo da frase;
- avaliação implícita;
- elogios irônicos;
- críticas indiretas;
- concessões positivas e negativas.

Exemplo:

> "Serve muito bem como peso de papel."

Apesar da presença de uma estrutura aparentemente positiva, o sentimento pretendido é negativo.

---

# 3. Dataset inicial

O primeiro dataset possuía 40 exemplos simples.

Exemplos:

> "Eu amei este produto!"

> "Que experiência horrível, nunca mais volto."

Nesse cenário, o Llama 3.1 8B atingiu:

**100% de acurácia no valset.**

Esse resultado tornou o benchmark inadequado, pois não havia espaço para medir melhoria produzida pelos optimizers.

O dataset foi então substituído por uma versão mais difícil com:

**60 exemplos**

Divisão:

| Conjunto | Exemplos |
|---|---:|
| Train | 36 |
| Validation | 12 |
| Holdout v1 | 12 |

---

# 4. Baseline

O programa inicial utilizado foi deliberadamente simples.

```python
class ClassificarSentimento(dspy.Signature):
    """Classifica uma frase como positiva ou negativa."""

    frase: str = dspy.InputField()
    classificacao: str = dspy.OutputField()
```

Resultado inicial no validation set:

**10/12 = 83,3%**

Os erros estavam concentrados principalmente em sentimentos indiretos.

Exemplo:

> "Nunca pensei que sentiria saudade do produto antigo até usar este."

Esperado:

`negativo`

Baseline:

`positivo`

Outro caso:

> "Achei que o problema fosse comigo até outras três pessoas terem o mesmo erro."

Esperado:

`negativo`

Baseline:

`positivo`

Isso confirmou que a nova tarefa possuía dificuldade suficiente para permitir otimização.

---

# 5. GEPA

O GEPA foi configurado usando:

- Llama 3.1 8B como task model;
- GPT-4.1 mini como reflection model;
- métrica com score e feedback textual;
- `auto="light"`.

A métrica retornava ao optimizer tanto uma pontuação quanto uma explicação textual sobre o erro.

Exemplo conceitual:

```python
dspy.Prediction(
    score=score,
    feedback=feedback
)
```

O objetivo era permitir que o GEPA analisasse erros e modificasse iterativamente a instrução.

---

# 6. Primeiro resultado do GEPA

No validation set, o GEPA encontrou um programa com:

**11/12 = 91,7%**

Comparação:

| Sistema | Validation |
|---|---:|
| Baseline | 83,3% |
| GEPA | 91,7% |

O optimizer produziu uma instrução muito mais detalhada.

Ela passou a incluir explicitamente:

- sarcasmo;
- ironia;
- contexto;
- sentimento predominante;
- críticas indiretas;
- diferença entre palavras isoladas e intenção geral.

A instrução também incorporou exemplos.

---

# 7. MIPROv2

O MIPROv2 foi executado como challenger.

Configuração:

- Llama 3.1 8B como task model;
- GPT-4.1 mini como prompt model;
- `auto="light"`;
- busca por instrução e exemplos few-shot.

Diferentemente do GEPA, o MIPROv2 podia otimizar simultaneamente:

**instruction + demonstrations**

Na primeira execução, o optimizer encontrou um programa com:

**12/12 = 100% no validation set.**

A solução possuía uma instrução relativamente curta e quatro demonstrações few-shot.

Exemplo de demonstração:

> "Tem muitos recursos; infelizmente nenhum funciona direito."

`negativo`

Outro exemplo:

> "Pensei em devolver, mas depois de configurar direito ficou excelente."

`positivo`

---

# 8. Holdout v1

O primeiro conjunto completamente isolado possuía:

**12 exemplos**

Resultados:

| Sistema | Resultado |
|---|---:|
| Baseline | 83,3% |
| GEPA | 75,0% |
| MIPROv2 | 100,0% |

Em números absolutos:

- Baseline: 10/12
- GEPA: 9/12
- MIPROv2: 12/12

O MIPROv2 apresentou ganho de:

**+16,7 pontos percentuais sobre o baseline.**

Entretanto, 12 exemplos foram considerados insuficientes para uma conclusão robusta.

---

# 9. Holdout v2

Foi criado um novo holdout independente contendo:

**30 exemplos**

Distribuição:

- 15 positivos;
- 15 negativos.

Nenhum optimizer foi alterado antes dessa avaliação.

Resultados:

| Sistema | Resultado |
|---|---:|
| Baseline | 80,0% |
| GEPA | 76,7% |
| MIPROv2 | 86,7% |

Resultados absolutos:

- Baseline: 24/30
- GEPA: 23/30
- MIPROv2: 26/30

O MIPROv2 novamente superou o baseline.

Ganho:

**+6,7 pontos percentuais.**

---

# 10. Necessidade de múltiplas seeds

Uma única execução de um optimizer pode produzir um resultado excepcional por acaso.

Por isso, o protocolo foi expandido para três execuções independentes de cada optimizer.

Foram utilizadas seeds diferentes.

Programas preservados:

```text
programa_gepa_run1.json
programa_gepa_run2.json
programa_gepa_run3.json

programa_miprov2_run1.json
programa_miprov2_run2.json
programa_miprov2_run3.json
```

---

# 11. Holdout v3

Foi criado um terceiro conjunto completamente novo.

Tamanho:

**60 exemplos**

Distribuição:

- 30 positivos;
- 30 negativos.

Foram avaliados:

- Baseline;
- GEPA run 1;
- GEPA run 2;
- GEPA run 3;
- MIPROv2 run 1;
- MIPROv2 run 2;
- MIPROv2 run 3.

---

# 12. Resultado principal

## Baseline

**73,3%**

44/60 exemplos corretos.

---

## GEPA

| Run | Score |
|---|---:|
| Run 1 | 68,3% |
| Run 2 | 73,3% |
| Run 3 | 85,0% |

**Média: 75,6%**

**Desvio padrão: 8,55**

Ganho médio sobre baseline:

**+2,3 pontos percentuais**

O resultado apresentou alta variabilidade entre seeds.

Uma execução ficou abaixo do baseline.

Uma execução ficou aproximadamente igual ao baseline.

Uma execução apresentou melhora substancial.

---

## MIPROv2

| Run | Score |
|---|---:|
| Run 1 | 88,3% |
| Run 2 | 86,7% |
| Run 3 | 93,3% |

**Média: 89,4%**

**Desvio padrão: 3,47**

Ganho médio sobre baseline:

**+16,1 pontos percentuais**

Todas as três runs superaram o baseline.

---

# 13. Comparação final

| Sistema | Média no Holdout v3 | Desvio |
|---|---:|---:|
| Baseline | 73,3% | — |
| GEPA | 75,6% | 8,55 |
| MIPROv2 | **89,4%** | **3,47** |

Dentro desta configuração experimental, o MIPROv2 apresentou simultaneamente:

- maior acurácia média;
- maior ganho sobre baseline;
- menor variabilidade entre seeds;
- ganho consistente em todas as execuções.

---

# 14. O que os optimizers aprenderam

A inspeção dos programas revelou estratégias diferentes.

## GEPA

As runs do GEPA produziram instruções bastante diferentes.

A run 1 gerou uma instrução longa com regras explícitas para:

- reclamações;
- sarcasmo;
- ironia;
- garantia;
- defeitos;
- expectativa frustrada.

A run 2 praticamente retornou ao baseline:

> "Classifica uma frase como positiva ou negativa."

A run 3 voltou a produzir uma instrução longa e detalhada.

Essa diferença entre as soluções ajuda a explicar a alta variabilidade observada.

---

## MIPROv2

As três runs do MIPROv2 produziram instruções diferentes, porém todas seguiram a mesma arquitetura:

- instrução relativamente curta;
- foco em contexto e nuances;
- quatro exemplos few-shot;
- exemplos próximos da fronteira de decisão.

Os exemplos envolviam principalmente:

- sarcasmo;
- contraste;
- negação;
- sentimentos mistos;
- mudança de polaridade ao longo da frase.

Isso sugere que a melhoria não dependeu de uma formulação específica de prompt.

O padrão mais consistente foi:

**instruction + demonstrations**

---

# 15. Análise dos erros

Alguns exemplos revelaram diferenças importantes entre as abordagens.

Frase:

> "O manual é provavelmente a parte mais funcional do produto."

Esperado:

`negativo`

Resultados:

- Baseline: erro
- GEPA run1: erro
- GEPA run2: erro
- GEPA run3: erro
- MIPROv2 run1: correto
- MIPROv2 run2: correto
- MIPROv2 run3: correto

Outro exemplo:

> "Tem aparência de produto premium e desempenho de protótipo."

Resultados:

- Baseline: erro
- GEPA run1: erro
- GEPA run2: erro
- GEPA run3: erro
- MIPROv2 run1: correto
- MIPROv2 run2: correto
- MIPROv2 run3: correto

Esses casos sugerem que as demonstrações few-shot ajudaram o modelo a interpretar estruturas implícitas difíceis de representar apenas por regras.

---

# 16. Overfitting comportamental do GEPA

Uma característica observada em algumas runs do GEPA foi uma tendência a atribuir peso excessivo a sinais negativos.

Exemplo:

> "A entrega atrasou, mas o suporte compensou e resolveu tudo rapidamente."

O sentimento final esperado é positivo.

Algumas versões do GEPA classificaram como negativo.

Isso sugere que instruções muito detalhadas sobre falhas, problemas e reclamações podem ter induzido o modelo a reagir excessivamente a qualquer elemento negativo da frase.

Esse comportamento não ocorreu de maneira tão consistente nas versões do MIPROv2.

---

# 17. Violação do espaço de saída

O baseline também apresentou outro tipo de erro:

`neutro`

Apesar da tarefa permitir somente:

- positivo;
- negativo.

Algumas versões do GEPA também apresentaram esse comportamento.

Os programas mais fortes do MIPROv2 reduziram significativamente esse problema.

Isso mostra que otimização pode melhorar não apenas a classificação semântica, mas também a aderência estrutural da saída.

---

# 18. Ambiguidade do benchmark

A análise pós-experimento identificou exemplos cujo próprio label pode ser discutível.

Exemplo:

> "Não travou nenhuma vez hoje. Um verdadeiro recorde."

O benchmark marcou como:

`negativo`

com intenção sarcástica.

Entretanto, a frase também possui interpretação literalmente positiva.

Todos os programas classificaram como positivo.

Outro exemplo:

> "O carregador funciona muito bem, o que é ótimo porque o aparelho precisa dele o tempo todo."

Também foi marcado como negativo por intenção sarcástica.

Todos os programas erraram.

Esses exemplos não foram alterados retroativamente.

A existência deles foi registrada como limitação metodológica.

---

# 19. Uso real dos modelos

Foi possível recuperar os dados diretamente do cache local do DSPy/LiteLLM.

## GPT-4.1 mini

Modelo:

`gpt-4.1-mini-2025-04-14`

Chamadas registradas:

**83**

Tokens de entrada:

**45.531**

Tokens de saída:

**21.776**

Total:

**67.307 tokens**

Cached input registrado:

**0 tokens**

---

## Llama 3.1 8B

Chamadas registradas:

**1.362**

Input tokens:

**974.423**

Output tokens:

**40.790**

Total:

**1.015.213 tokens**

Custo de API:

**US$ 0**

O processamento ocorreu localmente via Ollama.

---

# 20. Tokens totais processados

Somando professor e aluno:

**1.082.520 tokens**

Distribuição aproximada:

- GPT-4.1 mini: 67.307
- Llama 3.1 8B: 1.015.213

A maior parte da computação foi realizada localmente pelo modelo gratuito.

---

# 21. Custo da OpenAI

Preços utilizados no momento do experimento:

- input GPT-4.1 mini: US$ 0,40 / 1 milhão de tokens;
- output: US$ 1,60 / 1 milhão de tokens.

Cálculo:

Input:

45.531 × 0,40 / 1.000.000

= **US$ 0,0182124**

Output:

21.776 × 1,60 / 1.000.000

= **US$ 0,0348416**

Total:

**US$ 0,053054**

Aproximadamente:

# US$ 0,053

ou cerca de cinco centavos de dólar.

Esse valor representa todas as chamadas ao GPT-4.1 mini registradas no cache durante o experimento e não apenas o MIPROv2.

---

# 22. Tempo

O experimento não foi instrumentado desde o início com medição uniforme de wall-clock time.

Por isso, não é metodologicamente adequado calcular uma média precisa de velocidade entre os optimizers.

Tempos observados diretamente:

- GEPA run 2: aproximadamente 24 min 14 s;
- GEPA run 3: aproximadamente 22 min 18 s.

Outras execuções tiveram diferentes níveis de cache e não podem ser comparadas diretamente.

Essa limitação foi preservada no relatório em vez de estimar tempos retroativamente.

---

# 23. Resultado custo × ganho

O experimento completo utilizou aproximadamente:

**US$ 0,053 em API paga**

enquanto o aluno local processou mais de:

**1 milhão de tokens**

sem tarifa de API.

O MIPROv2 elevou o desempenho médio do Llama 3.1 8B no holdout principal de:

**73,3%**

para:

**89,4%**

Ganho:

# +16,1 pontos percentuais

O GEPA elevou o desempenho médio para:

**75,6%**

Ganho:

**+2,3 pontos percentuais**

---

# 24. Conclusão

Este experimento demonstrou que um modelo local relativamente pequeno pode apresentar melhora substancial quando seu comportamento é otimizado sistematicamente.

O Llama 3.1 8B passou de:

**73,3%**

para uma média de:

**89,4%**

com MIPROv2.

Isso representa aproximadamente:

**+16,1 pontos percentuais**

sem alterar pesos do modelo, realizar fine-tuning ou substituir o modelo de produção.

A melhoria ocorreu apenas através da otimização do programa DSPy.

---

# 25. GEPA vs MIPROv2

A hipótese inicial favorecia GEPA como optimizer principal.

O experimento mostrou resultado diferente.

Nesta tarefa específica:

**MIPROv2 apresentou melhor desempenho e maior estabilidade.**

Entretanto, isso não implica que MIPROv2 seja universalmente superior ao GEPA.

Os resultados são limitados a:

- classificação binária de sentimento;
- português brasileiro;
- Llama 3.1 8B;
- Ollama;
- GPT-4.1 mini;
- DSPy 3.3.1;
- dataset utilizado;
- budgets utilizados.

Outras tarefas podem favorecer abordagens diferentes.

---

# 26. Hipótese técnica resultante

A análise dos programas produzidos sugere uma hipótese:

> Para modelos locais menores, exemplos few-shot representativos podem transmitir fronteiras de decisão complexas de forma mais eficiente do que instruções extensas baseadas apenas em regras.

Neste experimento, o MIPROv2 repetidamente encontrou soluções baseadas em:

**instrução curta + quatro demonstrações**

enquanto o GEPA produziu instruções significativamente mais variáveis.

Essa hipótese não foi isolada experimentalmente e não deve ser interpretada como conclusão causal.

Ela constitui uma direção clara para experimentos futuros.

---

# 27. Limitações

O experimento possui várias limitações.

### Dataset sintético

Os exemplos foram construídos especificamente para testar estruturas linguísticas difíceis.

Resultados em dados reais podem ser diferentes.

### Tarefa única

Foi analisada apenas classificação de sentimento.

Não é possível generalizar diretamente para:

- extração;
- geração;
- agentes;
- RAG;
- programação;
- raciocínio complexo.

### Modelo único

Foi utilizado apenas Llama 3.1 8B como aluno.

### Professor único

Foi utilizado GPT-4.1 mini.

### Holdouts relativamente pequenos

Mesmo o maior conjunto possuía apenas 60 exemplos.

### Labels ambíguos

Algumas frases apresentaram interpretação subjetiva.

### Tempo não instrumentado

Não foi possível comparar formalmente velocidade de otimização.

---

# 28. Resultado principal em uma frase

> **Nesta tarefa, DSPy + MIPROv2 aumentou o desempenho médio de um Llama 3.1 8B local de 73,3% para 89,4%, com apenas cerca de US$ 0,053 de uso total registrado do modelo professor durante todo o experimento.**

---

# 29. Resultado conceitual

O experimento oferece evidência prática para uma ideia importante:

**a qualidade de um sistema baseado em LLM não depende apenas de escolher um modelo maior.**

Arquitetura, exemplos, métricas, avaliação e otimização do programa podem produzir ganhos significativos mesmo mantendo o mesmo modelo.

Nesse cenário:

```text
mesmo modelo
+ melhor programa
+ melhores demonstrações
+ avaliação estruturada
──────────────────────────
melhor desempenho
```

O modelo não mudou.

O sistema ao redor dele mudou.

E o comportamento final mudou junto.

---

# 30. Próximas linhas de pesquisa

Experimentos futuros podem investigar:

1. MIPROv2 com e sem demos para isolar o efeito do few-shot.
2. GEPA + demos pré-selecionadas.
3. Modelos locais menores, como 3B ou 1B.
4. Outros modelos locais além do Llama.
5. Tarefas de extração estruturada.
6. Classificação multiclasse.
7. Geração com avaliação semântica.
8. RAG.
9. Agentes com múltiplas etapas.
10. Comparação com fine-tuning.
11. Comparação custo de optimizer × aumento de qualidade.
12. Transferência do prompt otimizado entre modelos.
13. Generalização para datasets reais.

---

# 31. Status final

**Infraestrutura:** concluída  
**Baseline:** concluído  
**GEPA:** concluído  
**MIPROv2:** concluído  
**Múltiplas seeds:** concluído  
**Holdouts:** concluídos  
**Análise de erros:** concluída  
**Custo:** calculado  
**Limitações:** documentadas  
**Experimento:** concluído
