# DSPy Prompt Optimizer

Apresentação escrita do ciclo principal de uma pesquisa aplicada sobre otimização de prompts para classificar sentimento em português brasileiro. O aluno experimental foi o `llama3.1:8b` local via Ollama; o professor histórico foi o `gpt-4.1-mini`. O ciclo principal foi concluído e aceito em 17/10/2026.

## Materiais publicados

- [Apresentação de portfólio](apresentacao_projeto_t23_v1.md) — problema, arquitetura, experimento, ablação, resultados, aprendizado e limites.
- [Relatório do experimento histórico](relatorio_final.md) — baseline, GEPA e MIPROv2 no conjunto histórico.
- [Relatório da ablação](relatorio_ablacao_demos.md) — três pares de programas MIPROv2 com e sem demonstrações.

No Holdout v3 histórico (60 frases), o baseline acertou 44/60 (73,3%), as três execuções do GEPA tiveram média de 75,6%, e as três do MIPROv2, 89,4%. São resultados descritivos daquele conjunto e da métrica histórica. Não estabelecem superioridade geral de um otimizador.

Na ablação posterior (240 itens sintéticos), cada par acertou 239/240 com quatro demonstrações e 237/240 sem elas. A diferença foi de +0,8333 ponto percentual por par; o intervalo bootstrap pareado de 95% foi [−0,2778; +2,2222] p.p. Como inclui zero, a direção ficou inconclusiva pelo critério definido antes da avaliação. O estudo não demonstrou equivalência, ausência de efeito ou benefício de pelo menos 5 p.p.

A estimativa de **US$ 0,053054** cobre as chamadas registradas do professor no experimento histórico inteiro. Não é preço exclusivo do MIPROv2 nem custo total do projeto.

Esta publicação reúne **os textos finais**. Os relatórios citam pelo nome scripts, conjuntos, diários e tabelas do acervo local; esses arquivos brutos não integram esta edição pública. Portanto, este repositório permite ler a metodologia e os resultados documentados, mas não executar uma reprodução completa apenas com o conteúdo publicado. As edições públicas registram o aceite posterior do ciclo e tornam explícitas as referências a arquivos não incluídos; os artefatos históricos originais permanecem preservados localmente.

Os dados da ablação são sintéticos, com cotas artificiais e revisão por uma pessoa. Foram avaliados três programas fixos e uma geração por item e condição. Os resultados não demonstram generalização para textos reais nem repetibilidade das respostas em nova inferência. Os holdouts históricos e o teste congelado não devem orientar seleção ou reotimização de prompts.

Extensões opcionais são estudos futuros independentes e não fazem parte desta publicação.

