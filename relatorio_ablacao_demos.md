# Relatório da ablação de demos — DSPy Prompt Optimizer

**Versão:** edição pública do relatório 0.1. **Data do relatório:** 16/10/2026, America/Sao_Paulo.  
**Estado:** relatório T20, reprodução T21, revisão final T22 e apresentação T23 aceitos; ciclo principal encerrado em 17/10/2026. Esta edição pública preserva resultados e limites, com referências a arquivos brutos mantidas por nome.

## 1. Resultado principal

Nos **240 itens sintéticos em português**, com rótulos revisados pelo usuário, cada um dos três programas MIPROv2 preservados acertou **239 itens com suas quatro demos** e **237 sem elas**. As instruções permaneceram idênticas dentro de cada par. Não houve saída inválida nas seis condições.

A diferença média observada, definida como **com demos menos sem demos**, foi **+0,8333 ponto percentual**. O intervalo bootstrap pareado e estratificado de 95% foi **[−0,2778; +2,2222] p.p.** Pela regra fixada antes da avaliação, a **direção é inconclusiva**, pois o intervalo inclui zero. O saldo observado é favorável às demos; a análise não estabelece benefício de pelo menos 5 p.p., equivalência ou ausência de efeito.

A comparação estima a contribuição de remover as demos destes três programas prontos no procedimento executado. Seu alcance é limitado pelo benchmark autoral, pela revisão humana única com ressalvas de apresentação, pelo teto de acurácia e pelos oito IDs que discordam em algum par. Os dois itens que favorecem demos nas três runs concentram todo o saldo agregado de seis acertos.

Fontes principais: resultados pareados T16 (`t16_conferencia_pareada_v1.md`), incerteza T16 (`t16_incerteza_v1.md`), parecer T17 (`t17_parecer_metodologico_v1.md`), síntese qualitativa T18 (`t18_sintese_qualitativa_v1.md`) e recursos T19 (`t19_recursos_utilizados_v1.md`).

## 2. Contexto histórico e pergunta da nova avaliação

O [relatório histórico de 16/09/2026](relatorio_final.md) documenta a comparação de baseline, GEPA e MIPROv2 usando `llama3.1:8b` como aluno local e `gpt-4.1-mini` como professor das otimizações. No Holdout v3, registrou baseline de 73,3%, GEPA com média de 75,6% e MIPROv2 com média de 89,4%. Esses valores são contexto histórico, associados àquele conjunto, à métrica histórica e à configuração de então.

A nova pergunta foi: **quanto as demos contribuem nos três programas MIPROv2 preservados?** Foram comparadas cópias do mesmo programa/instrução com as quatro demos originais ou sem demos. Não houve nova otimização, seleção da melhor run ou participação do professor na inferência final. Baseline e GEPA ficaram fora da nova rodada, por decisão anterior à avaliação.

Reotimizar uma instrução para funcionar sem demos finais constitui outra pergunta. A ablação atual não determina se uma nova instrução otimizada conseguiria recuperar desempenho, nem explica causalmente o ganho histórico do MIPROv2. As acurácias atuais e históricas não devem ser comparadas como melhora ou regressão sob condições iguais.

## 3. Protocolo, condições e congelamento

O protocolo aprovado (`protocolo_ablacao_demos.md`) foi fixado pelo manifesto T09 v3 (`manifesto_congelamento_T09_v3.md`) e pelo registro de congelamento (`registro_congelamento_T09_v1.md`). O manifesto identifica dataset, ordem de IDs, programas, métrica, avaliador, configuração e análise por hashes. O congelamento é uma regra de integridade auditável, não uma mudança física de permissão de escrita.

| Condição | Programa serializado | Demos na inferência | Instrução |
| --- | --- | ---: | --- |
| C1 | `programa_miprov2_run1.json` | 4 | Original da run 1 |
| S1 | `programa_miprov2_run1_sem_demos.json` | 0 | Idêntica à de C1 |
| C2 | `programa_miprov2_run2.json` | 4 | Original da run 2 |
| S2 | `programa_miprov2_run2_sem_demos.json` | 0 | Idêntica à de C2 |
| C3 | `programa_miprov2_run3.json` | 4 | Original da run 3 |
| S3 | `programa_miprov2_run3_sem_demos.json` | 0 | Idêntica à de C3 |

As derivações preservam os demais campos funcionais; a mudança é esvaziar `demos`. Sua proveniência está no manifesto de referência (`manifesto_referencia.md`). Os identificadores de run nomeiam três programas fixos. Não constituem três novas otimizações executadas nesta ablação, e não se inventam seeds históricas ausentes.

A ordem congelada foi **C1, S1, S2, C2, C3, S3**, uma condição por execução. A inversão do par central reduz uma assimetria da ordem, mas não estima separadamente deriva temporal. Foi prevista uma rodada principal por item-condição, com retomadas de transporte controladas. Textos, rótulos, programas, ordem, métrica e análise permaneceram congelados após exposição às respostas. Os holdouts v1–v3 e o novo teste não podem orientar seleção ou ajuste de prompts.

## 4. Conjunto de teste e revisão humana

O arquivo final é teste_ablacao_t09_v1.jsonl (`teste_ablacao_t09_v1.jsonl`), com 240 IDs estáveis e ordem fixada em ordem_ablacao_t09_v1.json (`ordem_ablacao_t09_v1.json`). O conjunto reúne opiniões sintéticas curtas sobre produtos e serviços, em português brasileiro, produzidas e revisadas em dez lotes de 24 itens. Há 120 positivos e 120 negativos, com a seguinte composição:

| Grupo principal | Produto positivo | Produto negativo | Serviço positivo | Serviço negativo | Total |
| --- | ---: | ---: | ---: | ---: | ---: |
| Explícito | 20 | 20 | 20 | 20 | 80 |
| Negação/concessão/contraste | 20 | 20 | 20 | 20 | 80 |
| Indireto/ironia | 20 | 20 | 20 | 20 | 80 |
| **Total** | **60** | **60** | **60** | **60** | **240** |

As cotas são artificiais e não estimam a distribuição de opiniões de clientes reais. O grupo indireto inclui avaliação por consequências funcionais e utilidade implícita, além de ironia. O protocolo definiu alvo identificável, polaridade global defensável e exclusão de casos neutros ou irresolvidos; o critério de rotulagem não é escolher automaticamente a última oração.

O usuário revisou os rótulos e registrou pistas textuais, decisões e dúvidas antes da inferência final. Propostas do assistente não equivalem a validação humana independente. Houve um revisor humano, sem medida de concordância entre dois anotadores. A autoria assistida tinha exposição ao histórico, e os registros preservam ressalvas: pista interpretativa no item 077, alterações de leitura em itens do lote 03 e apresentação fora da ordem no lote 10. Não se afirma cegamento integral, dupla validação independente ou gabarito infalível.

A auditoria de proximidade T09 (`t09_auditoria_proximidade_v1.md`) e os registros de T08 documentam triagem lexical e qualitativa interna e frente a fontes anteriores, somente para controle de sobreposição. Isso não prova independência semântica/estatística universal ou ausência de contaminação no treinamento do modelo. Moldes narrativos e afinidades de estilo permanecem uma limitação.

Os 240 itens foram definidos como compromisso entre diversidade, revisão e processamento: **não houve cálculo formal de poder**. As 1.440 avaliações planejadas não são 1.440 observações independentes; a unidade de análise é o item com seus seis resultados associados. O tamanho não foi ampliado após os resultados. Os itens de desenvolvimento usados no piloto e nas checagens operacionais estão fora do teste final.

## 5. Aluno, configuração e métrica

| Componente | Configuração registrada | Fonte e limite |
| --- | --- | --- |
| Ambiente | Windows; Python 3.12.10; DSPy 3.3.1; LiteLLM 1.101.0; Ollama 0.40.1 | Cabeçalhos dos diários e evidência operacional do congelamento. |
| Aluno | `ollama_chat/llama3.1:8b`, local em `http://localhost:11434`; GGUF `Q4_K_M` | Digest fixado: `46e0c10c039e019119339687c3c1757cc81b9da49709a3b3924863ba87ca666e`. |
| Parâmetros solicitados | Temperatura 0; seed de geração 20260925; contexto 8192; saída máxima 256 tokens | A checagem de desenvolvimento capturou os parâmetros transmitidos; o backend não ecoou temperatura, seed e limite de saída internamente aplicados. Contexto ativo 8192 foi observado. |
| Adaptador | `ChatAdapter`, sem fallback automático para JSON | Campo de classificação extraído pelo caminho congelado do DSPy 3.3.1. |
| Cache/concorrência | Cache de resposta desativado; uma requisição por vez | `cache_hit` retornou nulo em todas as respostas finais; cache computacional interno não medido. |
| Transporte | Retries implícitos 0; até 3 tentativas controladas; esperas 5/15 s; timeout 300 s | Limites configurados, não provas de determinismo ou de comportamento sob toda falha. |

Somente `frase` entra como dado do item no programa. ID, rótulo esperado, grupo, domínio e justificativa humana permanecem metadados de avaliação. Nas condições C, as demos originais são a fonte de exemplos rotulados permitida no contexto. Instruções e demos não foram editadas pelos erros finais.

A métrica versionada (`metrica_ablacao_v1.py`) avalia o campo `classificacao` extraído pelo adaptador. Aplica somente `strip().lower()` e exige correspondência exata com `positivo` ou `negativo`. Assim, ` POSITIVO ` é válido; `positivo.`, `positivo ou negativo`, `não é positivo`, rótulo neutro, vazio ou não textual são inválidos. Não se aceita resposta ambígua apenas por conter uma dessas palavras.

Uma saída válida correta recebe 1; válida incorreta ou inválida recebe 0, mantendo o denominador. Falha operacional sem resposta recuperável fica sem score, com tratamento próprio. A resposta bruta inteira é preservada: texto adicional, inclusive após o marcador `completed`, não é confundido com o campo extraído nem apagado. Comentários emitidos não são evidência confiável do processo interno do modelo.

## 6. Resultados pareados e validade

| Run | Acertos com demos/N | Acurácia com demos | Acertos sem demos/N | Acurácia sem demos | Delta com − sem (p.p.) | Inválidas com/sem |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | 239/240 | 99,5833% | 237/240 | 98,7500% | +0,8333 | 0/0 |
| 2 | 239/240 | 99,5833% | 237/240 | 98,7500% | +0,8333 | 0/0 |
| 3 | 239/240 | 99,5833% | 237/240 | 98,7500% | +0,8333 | 0/0 |

| Run | Ambas acertam | Só com demos acerta | Só sem demos acerta | Ambas erram | N |
| --- | ---: | ---: | ---: | ---: | ---: |
| 1 | 236 | 3 | 1 | 0 | 240 |
| 2 | 236 | 3 | 1 | 0 | 240 |
| 3 | 236 | 3 | 1 | 0 | 240 |

Retirar demos gerou três perdas e um ganho de acerto por par, um saldo de dois acertos favoráveis a mantê-las. O saldo agregado de seis acertos corresponde aritmeticamente a `100 × 6 / (3 × 240) = 5/6 p.p.`; não aumenta a unidade de análise para 720 itens independentes.

Há quatro IDs discordantes por par e oito distintos na união. Os demais **232 IDs foram acertados nas seis condições**. Não houve mudança de validade. Término `stop` foi reportado em todas as 1.440 respostas recebidas, sem provar por si só ausência de truncamento de entrada. Os scores e suas referências ao texto bruto estão em t16_pareamento_v1.csv (`t16_pareamento_v1.csv`); os totais e auditorias, em t16_resultados_pareados_v1.json (`t16_resultados_pareados_v1.json`).

## 7. Incerteza e interpretação pré-especificada

O desfecho primário dá peso igual aos três programas. Para cada ID, `d[i]` é a média das três diferenças de score com menos sem demos; `Delta_medio = 100 × média_i d[i]`. O limiar de interesse fixado foi **5 p.p. em valor absoluto**.

O bootstrap reamostrou 20 IDs com reposição em cada uma das 12 células, mantendo juntas as seis respostas do mesmo ID. Foram usadas 10.000 réplicas, `random.Random(20260927)`, células e IDs em ordem lexicográfica e percentis 2,5/97,5 interpolados nas posições `(B−1) × q`. Não foram reamostrados programas nem respostas individuais independentes. Os dados, réplicas e parâmetros estão em t16_bootstrap_v1.json (`t16_bootstrap_v1.json`) e t16_bootstrap_replicas_v1.csv (`t16_bootstrap_replicas_v1.csv`).

Os portões numéricos aprovados passaram: réplicas finitas e dentro dos limites, extremos ordenados e intervalo não colapsado. As réplicas tiveram 37 valores distintos. Os limites exatos do intervalo foram `−5/18` e `20/9` p.p.; o ponto observado é `5/6` p.p. A distribuição observada por item foi:

| d[i] | Quantidade de IDs |
| ---: | ---: |
| −1/3 | 3 |
| 0 | 232 |
| +1/3 | 3 |
| +1 | 2 |

Como o intervalo **[−0,2778; +2,2222] p.p. inclui zero**, a direção é inconclusiva pela seção 9.3 do protocolo. Estar dentro de −5 a +5 p.p. permite descrever compatibilidade com efeitos pequenos **sob este procedimento**; não equivale a teste formal de equivalência, ausência de efeito ou exclusão de efeitos maiores em outras distribuições. A designação 95% não é probabilidade de que o efeito verdadeiro esteja neste intervalo particular.

O intervalo aproxima variação ao reamostrar itens semelhantes sob as cotas, condicionado aos três programas fixos. Não cobre viés de autoria/rotulagem, dependência residual, novas seeds de otimização, repetição de gerações ou textos reais. Os poucos discordantes e a ausência de avaliação específica da cobertura do método neste regime limitam sua interpretação externa, mesmo com reprodução numérica correta.

O parecer T17 (`t17_parecer_metodologico_v1.md`) registra ainda o teto: com a referência sem demos em 98,75%, o ganho positivo máximo aritmético no conjunto observado seria **1,25 p.p.** se a contraparte acertasse tudo. É diagnóstico posterior às respostas, não cálculo de poder ou justificativa para reduzir o limiar. Esse teto da contagem fixa **não limita o bootstrap a 1,25 p.p.**, pois a reamostragem repondera itens. O intervalo superior de 2,2222 p.p. é coerente com o método.

## 8. Descritivos secundários

Cada célula de run abaixo informa acertos **com/sem demos**, ambos sobre o N da linha. Os três eixos repartem os mesmos itens; não devem ser somados como amostras adicionais. Fonte: t16_descritivos_v1.csv (`t16_descritivos_v1.csv`).

| Eixo/nível | N por condição | Run 1 | Run 2 | Run 3 | Delta médio (p.p.) |
| --- | ---: | ---: | ---: | ---: | ---: |
| Rótulo negativo | 120 | 119/120 | 120/120 | 120/119 | 0,0000 |
| Rótulo positivo | 120 | 120/117 | 119/117 | 119/118 | +1,6667 |
| Produto | 120 | 119/119 | 119/118 | 120/118 | +0,8333 |
| Serviço | 120 | 120/118 | 120/119 | 119/119 | +0,8333 |
| Explícito | 80 | 80/80 | 80/80 | 80/80 | 0,0000 |
| Indireto/ironia | 80 | 79/78 | 80/77 | 79/77 | +2,5000 |
| Negação/concessão/contraste | 80 | 80/79 | 79/80 | 80/80 | 0,0000 |

Os recortes foram pré-especificados e permanecem descritivos. Não houve novos testes ou intervalos confirmatórios por subgrupo. O delta de +2,5 p.p. em indireto/ironia não demonstra mecanismo específico ou interação confirmada; o grupo explícito atingiu 100% em todas as condições.

## 9. Análise qualitativa e concentração do saldo

T18 catalogou todos os casos em que retirar demos mudou acerto ou validade. Por run, os exemplos foram selecionados por ID crescente até o limite protocolar de três por direção; houve três perdas e um ganho em cada uma, sem mudança de validade. Portanto, todos os casos discordantes foram cobertos. A tabela integra a união dos catálogos. “Perda” e “ganho” referem-se a **retirar demos**; “ambas acertam” significa score 1 nas duas versões.

| ID | Cenário | Rótulo congelado | Run 1 | Run 2 | Run 3 |
| --- | --- | --- | --- | --- | --- |
| abl_t08_009 | Cortina | positivo | ambas acertam | ganho | ambas acertam |
| abl_t08_020 | Envelopes | negativo | ambas acertam | ambas acertam | perda |
| abl_t08_050 | Conserto das botas | positivo | ambas acertam | ambas acertam | ganho |
| abl_t08_077 | Recreadora | positivo | perda | perda | perda |
| abl_t08_130 | Estojo de lápis | negativo | ganho | ambas acertam | ambas acertam |
| abl_t08_164 | Marcador de carreira do tricô | positivo | ambas acertam | perda | ambas acertam |
| abl_t08_210 | Espetáculo sem celulares | positivo | perda | ambas acertam | ambas acertam |
| abl_t08_234 | Medidor de pressão dos pneus | positivo | perda | perda | perda |

São **12 ocorrências de mudança em oito IDs**, nove favoráveis a manter demos e três favoráveis à remoção. Os IDs 077 e 234 favorecem demos nos três pares e produzem seis acertos líquidos; os outros seis IDs somam três mudanças em cada direção, saldo zero. Essa decomposição descritiva mantém todos os itens. Não foi realizada sensibilidade excluindo o 077 ou qualquer outro caso.

O medidor e o marcador de tricô ilustram satisfação expressa por utilidade na rotina. Recreadora e espetáculo ilustram avaliação favorável apesar de formulação superficialmente negativa. O item 077 exige a ressalva de que recebeu pista interpretativa durante a revisão humana; seu rótulo congelado foi preservado. A recorrência é observação do catálogo e não prova um mecanismo interno do aluno.

As contraprovas impedem uma conclusão unilateral. Na cortina, C2 enfatiza aspereza apesar da avaliação favorável e erra; S2 acerta. No estojo, C1 descreve positivamente a situação humorística de ruptura/bagunça, enquanto S1 acerta negativo. Nos envelopes, S3 atribui satisfação a um conserto compensatório que não estabelece aprovação do produto. Nas botas, C3 enfatiza o defeito anterior em vez do resultado favorável do serviço, enquanto S3 acerta.

Explicações emitidas podem conter fatos acrescentados: no 077, C2 troca a frequência semanal por diária, e C3 acrescenta elementos de brinquedoteca/compra. Um rótulo correto não torna esses comentários factualmente fiéis. Idioma, presença e qualidade dos comentários não se tornaram nova métrica; as observações não constituem comparação global de raciocínio. Frases, justificativas humanas, respostas integrais, linhas dos diários e hashes permanecem no catálogo integrado (`t18_catalogo_integrado_v1.json`) e nos recortes da run 1 (`t18_run1_analise_qualitativa_v1.md`), run 2 (`t18_run2_analise_qualitativa_v1.md`) e run 3 (`t18_run3_analise_qualitativa_v1.md`).

## 10. Execução, falhas e recursos

Cada condição encerrou com 240 respostas pontuadas, sem evento `falha_operacional` nem pendência final. C3 preserva uma interrupção: o item `abl_t08_168`, tentativa 1, ficou sem resposta recuperável. A retentativa foi liberada com evidência de backend inativo e a tentativa 2 produziu o único score terminal. São **241 requisições em C3**; não se afirma execução exatamente uma vez no backend ou identidade com a resposta perdida.

| Condição | Requisições | Respostas/scores | Janela da condição (s) | Soma das chamadas com resposta (s) | Tokens entrada | Tokens saída |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| C1 | 240 | 240 | 5.579,215 | 5.254,518 | 115.721 | 14.145 |
| S1 | 240 | 240 | 2.949,342 | 2.537,089 | 69.881 | 4.641 |
| S2 | 240 | 240 | 2.764,751 | 2.327,276 | 79.961 | 3.840 |
| C2 | 240 | 240 | 5.663,363 | 5.326,149 | 124.601 | 13.241 |
| C3 | 241 | 240 | 6.486,211 | 5.791,721 | 107.561 | 14.834 |
| S3 | 240 | 240 | 3.821,107 | 3.570,130 | 65.321 | 8.583 |
| **Total registrado** | **1.441** | **1.440** | **27.263,989** | **24.806,884** | **563.046** | **59.284** |

Fonte: quadro T19 (`t19_recursos_utilizados_v1.md`) e diários identificados nele. Tokens de entrada mais saída totalizam **622.330**, relativos às respostas recuperadas. Duração e tokens da tentativa interrompida são desconhecidos, sem preenchimento por zero. Requisição é registro do cliente anterior à chamada, não captura independente de cada envio HTTP final.

A janela de cada condição vai da criação do diário ao último score; sua soma é aproximadamente 7 h 34 min 24 s. A soma das chamadas com resposta, medida pelo adaptador, é aproximadamente 6 h 53 min 27 s. Do primeiro diário ao último score decorreram 36.956,727 s, cerca de 10 h 15 min 57 s, incluindo os intervalos entre condições. Carga do modelo, pausa, espera humana, persistência e processamento não foram decompostos completamente. Com uma rodada, ordem fixa e cache interno não medido, esses tempos não são benchmark causal de velocidade.

O cache de respostas foi configurado como desativado. `cache_hit=null` nas 1.440 respostas significa informação indisponível, não 1.440 misses observados. O aviso de falha de inicialização do cache em disco do DSPy, com fallback para memória, permanece documentado; isoladamente, não prova reaproveitamento de resposta. As capturas de desenvolvimento demonstram geração nova nas respectivas checagens, sem substituir captura por item final.

Piloto T07, falhas de transporte prévias e checagens T09 estão separados no quadro T19. O teste final usou somente o aluno local, sem chamada a professor nos diários e no caminho preservado. Não houve acesso a faturamento. Ausência de tarifa de API externa não implica custo total zero: consumo de CPU/GPU, RAM/VRAM e energia não foram medidos; hardware, tempo humano e uso do assistente de chat não foram monetizados.

O custo aproximado histórico de **US$ 0,053** (estimativa detalhada US$ 0,053054) pertence ao conjunto de chamadas do professor recuperadas do cache no experimento anterior. Não é custo da ablação, de um único optimizer ou fatura conciliada, e não foi somado aos recursos desta rodada.

## 11. Limitações e alcance

1. **Distribuição artificial e autoria:** textos sintéticos balanceados não representam frequências reais; afinidades de estilo e dependência residual não são corrigidas automaticamente pela estratificação.
2. **Rótulos e apresentação:** um revisor humano, autoria assistida com exposição prévia e ressalvas documentadas de leitura/pistas. Não há concordância interanotadores ou validação externa do gabarito.
3. **Teto e poucos discordantes:** sem demos já houve 98,75%; oito IDs concentram as mudanças. Reamostrar esses valores não revela erros ausentes do benchmark.
4. **Três programas fixos e geração única:** deltas iguais não demonstram estabilidade entre novas seeds do optimizer, determinismo ou repetibilidade do backend.
5. **Efeito total da retirada:** remover demos muda conjuntamente exemplos, conteúdo, comprimento e estrutura do contexto. O desenho não separa mecanismos nem desempenho de estratégias reotimizadas.
6. **Execução temporal e retomada:** ordem e deriva temporal não foram estimadas separadamente. C3 tem consumo desconhecido de uma tentativa e uma retentativa autorizada.
7. **Configuração e computação parcialmente observadas:** valores transmitidos na checagem não foram todos ecoados como efetivamente aplicados; cache interno, carga e hardware não têm telemetria completa.
8. **Incerteza condicional:** o intervalo segue o método aprovado e passou pelos controles numéricos, sem certificar cobertura numa população real, corrigir vieses ou provar equivalência. O conjunto não foi ampliado, os rótulos não foram revistos e o método não foi trocado para buscar um vencedor.

## 12. Conclusão e pendências

Neste benchmark, manter as quatro demos originais correspondeu a dois acertos líquidos adicionais por programa e a **+0,83 p.p. de diferença média observada**. O intervalo de **−0,28 a +2,22 p.p.** produz direção inconclusiva pela regra anterior às respostas. O achado é compatível com efeitos pequenos sob a reamostragem adotada, com as limitações declaradas. Não sustenta afirmar que demos são dispensáveis, que sempre ajudam ou que explicam a vantagem histórica do MIPROv2.

A ablação respondeu à pergunta sobre estes três programas preservados e expôs pouco espaço de melhoria nesta distribuição. Uma investigação futura de reotimização sem demos ou de textos reais exige decisão, protocolo e dados independentes próprios; nenhuma extensão foi iniciada. Resultado inconclusivo permite concluir este estudo quando documentação e revisões forem aceitas.

**Estado após a revisão:** T20–T23 foram aceitas e o ciclo principal foi encerrado. Não há correção numérica obrigatória identificada por T17. Não foram feitos novos testes de hipótese, intervalos alternativos, cálculo retrospectivo de poder, sensibilidade ou inferência para este relatório.

## 13. Rastreabilidade desta versão

Os documentos de etapas anteriores conservam seus estados no momento da entrega; indicações antigas como “pendente” não substituem os aceites posteriores desta conversa e do Registro. Os números do relatório derivam dos artefatos abaixo e das fontes congeladas que eles identificam.

| Evidência | SHA-256 conferido para esta versão |
| --- | --- |
| Protocolo (`protocolo_ablacao_demos.md`) | `2146437ed8534339d01f4820b4c9b027a7f7686eb6f213446126d8b170283cfb` |
| Manifesto congelado T09 v3 (`manifesto_congelamento_T09_v3.md`) | `9ea1385ad6f4b703d980a2e6caf3edd3144de13c06a927f6030ff80f34126cd8` |
| Resultados pareados (`t16_resultados_pareados_v1.json`) | `7702aaac901ce6bfbdebebfdbc9aa38c107064f533207c6aec13742c4f5117c9` |
| Pareamento por ID (`t16_pareamento_v1.csv`) | `03c23156a232063092c6027b29586e4a2abbf25249515fd56917f84115fca711` |
| Resultado bootstrap (`t16_bootstrap_v1.json`) | `d47c41210c133b58ab664ac66c41e37ce51e6672a14431517cf118dcede50ce4` |
| Descritivos (`t16_descritivos_v1.csv`) | `add01e3d9031f1259f816dcaadc8fe2e44bbf1b7ad113e3f34e531188ce76712` |
| Parecer metodológico T17 (`t17_parecer_metodologico_v1.md`) | `afd472dd0b02ecc33e2da43fd9260a53382a68ea10cd3f5eb2490039ed2a3c54` |
| Síntese qualitativa T18 (`t18_sintese_qualitativa_v1.md`) | `25f7503d7e359d4c2ace2a4f3f41972af347004b016c2bbe7a3dd1a35bda049b` |
| Catálogo integrado T18 (`t18_catalogo_integrado_v1.json`) | `c6913997b7bec4077c54e3db0503928e77539dbbdd7cdd874a7a57c52a3e69e4` |
| Recursos T19 (`t19_recursos_utilizados_v1.md`) | `714f76d1852e829232ac6565641cdfde1770724fcba534f7b0c53c3142806f3d` |
| [Relatório histórico preservado](relatorio_final.md) | `b4f4d172d861d03334b573b59fed75305e46642471e1b5b04d4578a2739fd287` |

Na preparação e conferência desta versão, as leituras quantitativas foram offline, com bloqueio de rede e sem importar DSPy. Foram conferidas as 40 fontes pelo carregador de T16, os hashes de fontes do catálogo T18, as referências numéricas às tabelas e os links locais. Não foi repetida a bateria de testes de implementação já aprovada, pois a alteração desta entrega é documental. Arquivos históricos, respostas, scores, programas e protocolo permanecem preservados.

