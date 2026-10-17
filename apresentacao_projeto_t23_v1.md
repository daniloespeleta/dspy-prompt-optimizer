# DSPy Prompt Optimizer — otimização de um modelo local e avaliação dos seus limites

**Texto de portfólio · versão pública 1.0 · 17/10/2026 · ciclo principal aprovado.**

O projeto investigou como melhorar a classificação de sentimento de um modelo local sem alterar seus pesos. No experimento histórico, os programas produzidos pelo MIPROv2 alcançaram média de **89,4% de acurácia**, frente a **73,3% do baseline**, no Holdout v3 de 60 frases. Uma ablação posterior examinou a contribuição das demonstrações nesses programas: a diferença observada foi de **+0,8333 ponto percentual**, com intervalo bootstrap de 95% **[−0,2778; +2,2222] p.p.** A direção permaneceu inconclusiva pelo critério definido antes da avaliação. Os dois estudos respondem a perguntas diferentes e têm limites próprios.

## O problema e a arquitetura

Classificar opiniões exige interpretar intenção, contexto, negação e ironia. Uma frase pode conter um elogio aparente e expressar insatisfação; também pode mencionar um problema e terminar com uma avaliação favorável. A tarefa foi classificar frases em português brasileiro como `positivo` ou `negativo`, usando o Llama 3.1 8B executado localmente via Ollama.

A arquitetura professor–aluno reservou ao **GPT-4.1 mini** o papel de auxiliar a otimização histórica e ao **Llama 3.1 8B** a execução da classificação. O DSPy 3.3.1 organizou o programa, a métrica e a busca de instruções e exemplos, em ambiente Windows com Python 3.12. O GEPA usou o professor para reflexão sobre erros; o MIPROv2, para propor instruções, em uma busca que também selecionava demonstrações: exemplos de entrada e resposta incluídos no contexto do aluno.

O que mudou foi o programa enviado ao modelo, com suas instruções e exemplos. Não houve fine-tuning dos pesos. A expressão professor–aluno descreve esses papéis no procedimento de otimização, sem implicar treinamento de um novo modelo.

Os **assistentes de IA usados no desenvolvimento, na revisão e na documentação do projeto** têm outra função. Eles não são o professor ou o aluno experimental. Recomendações de modelo e esforço nos roteiros referem-se ao assistente do chat; não identificam modelos executados no experimento nem comprovam seu uso. Na ablação final, o caminho preservado utilizou somente o aluno local, sem chamadas ao professor. [Arquitetura e histórico](relatorio_final.md); [configuração e execução da ablação](relatorio_ablacao_demos.md).

## O experimento histórico

O conjunto inicial era simples demais: o aluno atingiu 100% na validação, sem espaço para medir melhoria. O estudo passou a usar 60 exemplos mais difíceis, divididos em 36 de treino, 12 de validação e 12 de Holdout v1. Depois, os programas foram avaliados nos holdouts v2 e v3, de 30 e 60 exemplos. Esses conjuntos e resultados foram preservados.

A comparação incluiu um baseline de instrução simples, GEPA e MIPROv2, estes com três execuções históricas preservadas e configuração `auto="light"`. No Holdout v3, os registros mostram:

| Sistema | Acurácias registradas | Média | Diferença média ante o baseline |
| --- | --- | ---: | ---: |
| Baseline | 73,3% — 44/60 | 73,3% | — |
| GEPA | 68,3%; 73,3%; 85,0% | 75,6% | +2,3 p.p. |
| MIPROv2 | 88,3%; 86,7%; 93,3% | 89,4% | +16,1 p.p. |

As três execuções do MIPROv2 superaram o baseline nesse conjunto. As execuções do GEPA variaram mais, contrariando a expectativa inicial de que ele seria a abordagem principal. A métrica histórica reconhecia os rótulos pela presença de `positiv` ou `negativ` no texto normalizado; era mais permissiva que a métrica estrita adotada depois. São resultados descritivos desta tarefa, destes dados e destes orçamentos; não estabelecem superioridade universal de um optimizer. Os registros não permitem reconstruir integralmente seeds e configuração histórica. [Relatório histórico](relatorio_final.md); resultados preservados (`resultado_experimento_3.txt`); métrica histórica (`metrica.py`).

As soluções MIPROv2 continham quatro demonstrações cada. Essa observação motivou uma pergunta adicional: quanto esses exemplos contribuíam para o desempenho dos programas já produzidos? O histórico, sozinho, não isolava essa contribuição.

## A ablação: retirar os exemplos mantendo a instrução

Para cada um dos três programas MIPROv2, foi preparada uma cópia com a mesma instrução e os demais campos preservados, retirando apenas as quatro demonstrações. Os três pares foram avaliados em um novo teste sintético de **240 itens**, com rótulos revisados pelo usuário antes da inferência final. O conjunto foi balanceado por polaridade, produto ou serviço e três grupos linguísticos: explícito; negação, concessão ou contraste; indireto ou ironia.

Protocolo, dados, rótulos, programas, ordem, métrica e análise foram congelados antes da avaliação. Cada condição teve um diário com requisições, respostas brutas e pontuações vinculadas por ID e hashes. A métrica exigiu que o campo de classificação extraído correspondesse exatamente a um dos dois rótulos após normalização de espaços externos e caixa. Falhas de transporte ficaram separadas dos erros de classificação.

Não houve nova otimização ou escolha da melhor run. A pergunta foi o efeito de retirar demos desses programas fixos, incluindo a mudança de conteúdo e comprimento do contexto. O desenho não compara estratégias novamente otimizadas para funcionar com ou sem exemplos. [Desenho e controles](relatorio_ablacao_demos.md); protocolo (`protocolo_ablacao_demos.md`).

## O resultado e a incerteza

| Programas comparados | Com quatro demos | Sem demos | Diferença com − sem |
| --- | ---: | ---: | ---: |
| Par 1 | 239/240 — 99,5833% | 237/240 — 98,7500% | +0,8333 p.p. |
| Par 2 | 239/240 — 99,5833% | 237/240 — 98,7500% | +0,8333 p.p. |
| Par 3 | 239/240 — 99,5833% | 237/240 — 98,7500% | +0,8333 p.p. |

Não houve saída inválida nas seis condições. Em cada par, retirar demos produziu três perdas e um ganho de acerto: dois acertos líquidos a favor de mantê-las.

A análise usou **10.000 réplicas de bootstrap pareado e estratificado**, preservando juntas as seis pontuações de cada item e as cotas do conjunto. A unidade de análise foi o item; as 1.440 avaliações não foram tratadas como observações independentes. O intervalo de 95% foi **[−0,2778; +2,2222] p.p.** Como inclui zero, a direção é **inconclusiva** pela regra pré-especificada. O estudo não estabeleceu benefício de pelo menos 5 p.p., equivalência ou ausência de efeito. Resultados pareados (`t16_resultados_pareados_v1.json`); bootstrap (`t16_bootstrap_v1.json`); interpretação revisada (`t17_parecer_metodologico_v1.md`).

A análise qualitativa encontrou oito IDs com mudança em algum par; 232 itens foram acertados nas seis condições. Dois IDs concentram todo o saldo agregado favorável às demos. Um deles, o item 077, tem ressalva documentada de pista interpretativa durante a revisão humana. Nenhum item ou rótulo foi alterado para mudar a conclusão. O desempenho sem demos já era de 98,75%, com pouco espaço para ganho no conjunto observado. Esses resultados também não permitem interpretar os 99,58% da ablação como evolução comparável aos 89,4% históricos: conjuntos, métricas e condições diferem. Síntese qualitativa (`t18_sintese_qualitativa_v1.md`); [relatório da ablação](relatorio_ablacao_demos.md).

## Recursos e limitações

O custo histórico estimado do professor foi **US$ 0,053054**, aproximadamente US$ 0,053, calculado com os preços históricos registrados e os tokens de 83 chamadas recuperadas do cache. O valor cobre o uso registrado do professor **no experimento histórico inteiro**, não apenas MIPROv2; não é fatura conciliada nem estimativa do custo completo do projeto. O aluno local não teve tarifa de API externa, mas energia, hardware, tempo humano e uso dos assistentes de desenvolvimento não foram monetizados. Estimativa histórica (`custo_experimento.txt`); quadro de recursos (`t19_recursos_utilizados_v1.md`).

A ablação registrou 1.441 requisições e 1.440 respostas pontuadas. A diferença corresponde a uma tentativa interrompida em C3, preservada como desconhecida, seguida de retentativa autorizada. Seu consumo não recuperado permanece desconhecido. Tempos registrados, cache parcialmente observado e ausência de telemetria completa impedem uma comparação causal de velocidade ou custo computacional entre condições.

O alcance científico também é limitado: dados sintéticos com cotas artificiais, um revisor humano, autoria assistida exposta ao histórico, afinidades de estilo e ressalvas de apresentação. Não houve dupla anotação independente ou cegamento integral. Três programas fixos e uma rodada por item-condição não estimam variabilidade entre novas otimizações ou repetibilidade de gerações. Parâmetros transmitidos ao backend não comprovam todos os valores internamente aplicados; `cache_hit=null` significa informação indisponível. O intervalo bootstrap não corrige esses limites nem certifica generalização para opiniões reais.

A documentação permite reanalisar respostas salvas, e a revisão T22 verificou essa análise no ambiente disponível sem identificar divergência material. Reconstrução em ambiente limpo e repetição das mesmas respostas em nova inferência não foram demonstradas. Os holdouts v1–v3 e o teste congelado permanecem fora da seleção, modificação ou reotimização de prompts. Guia de reprodução (`reproducao_ablacao_demos.md`); parecer final T22 (`t22_parecer_final_v1.md`).

## Aprendizado metodológico e próximos estudos

O projeto reúne implementação de avaliação, comparação de programas, revisão humana e documentação de evidências. Seu aprendizado central foi transformar uma hipótese atraente em uma pergunta controlada e preservar a conclusão mesmo quando o resultado ficou inconclusivo. Separar ganho observado, contribuição de um componente e generalização evitou atribuir às demonstrações toda a vantagem histórica do MIPROv2.

O roteiro já prevê estudos opcionais, entre eles reotimizar programas sem demos finais e avaliar textos reais com protocolo e dados próprios. São perguntas futuras, sem execução autorizada nesta apresentação. Não reutilizam os testes congelados para escolher novas soluções. Roteiro de extensões (`roteiro_t19_t23_extensoes_a_k.md`).

T22 e T23 foram aceitas, e o usuário confirmou o encerramento do ciclo principal em 17/10/2026. Esta edição pública registra esse estado sem mudar os resultados ou seus limites. As extensões opcionais permanecem propostas para ciclos independentes.

