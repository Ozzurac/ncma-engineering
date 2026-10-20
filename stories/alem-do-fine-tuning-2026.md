# Além do Fine-Tuning: o que aprendemos construindo sistemas de IA na prática

> **NCMA Engineering | Notas de pesquisa aplicada | Setembro–outubro de 2026**  
> **Autor:** Valter Lourenço Junior, laboratório independente NCMA Systems.  
> **Escopo:** retrospectiva técnica baseada em experimentos delimitados, não certificação de produção.  
> [English version](beyond-fine-tuning-2026.md)

## O documento original explicava as ferramentas. Os experimentos mostraram os limites.

Em setembro, organizamos um whitepaper sobre o caminho de uma LLM isolada até um sistema de IA operacional. Ele reunia treinamento, quantização, LoRA, inferência, contexto, memória, recuperação, ferramentas, percepção e governança.

Era um mapa conceitual útil, mas tinha uma pergunta que só os testes poderiam responder: **o que realmente melhora quando aplicamos essas técnicas a uma tarefa concreta?**

Nas semanas seguintes, encontramos cinco distinções que mudaram nossa forma de trabalhar:

- Treinar um modelo não é a mesma coisa que melhorar uma aplicação.
- Preservar um comportamento após quantização não significa que o comportamento seja adequado.
- Gerar mais tokens por segundo não garante concluir uma resposta.
- Ter uma janela de contexto maior não significa conseguir terminar uma tarefa longa.
- Propor uma ferramenta não dá ao modelo autorização para executá-la.

Os resultados positivos foram importantes. Os negativos foram decisivos.

## 1. Um fine-tuning com objetivo claro produziu ganho expressivo

Adaptamos um modelo de reconhecimento de fala para enunciados técnicos em português brasileiro. Este experimento era de **ASR**, separado dos testes de LoRA em LLMs.

Organizamos treinamento e replay, um conjunto de desenvolvimento de 40 exemplos para selecionar o checkpoint, um *holdout* de domínio congelado com 80 exemplos e uma amostra independente de 24 áudios FLEURS pt-BR para observar regressões em linguagem geral.

A terceira época reduziu a *training loss*, mas não melhorou as métricas que justificariam escolhê-la. Mantivemos a segunda época e só então avaliamos o holdout.

| Métrica no holdout | Modelo inicial | Checkpoint selecionado |
| --- | ---: | ---: |
| Word Error Rate | **21,831%** | **2,1127%** |
| Entidades exatas | 22/80 | **73/80** |
| Transcrições integralmente exatas | 20/80 | **73/80** |

O ganho foi forte, dentro do domínio e da amostra avaliados. Isso não demonstra acurácia universal em áudios reais, ruído, sotaques ou outras condições. O resultado tampouco aprova por si só um assistente de voz completo.

**O que aprendemos:** especialização funciona quando a tarefa é bem definida, a escolha de checkpoint usa desenvolvimento separado e a avaliação evita contaminar o holdout.

[Registro técnico e protocolo de avaliação](../projects/fine-tuning-evaluation.md).

## 2. Nosso LoRA melhorou o diagnóstico, mas não tornou a LLM confiável

Em outro trabalho, treinamos um adapter LoRA supervisionado para um modelo local Qwen3.5-4B.

Avaliamos cada passagem do artefato: adapter PEFT, pesos fundidos, GGUF F16 e Q4_K_M. O objetivo era saber se o comportamento aprendido se preservava ao preparar o modelo para inferência.

| Estado do artefato | Acertos em 48 tarefas diagnósticas |
| --- | ---: |
| Controle não refinado | 0 |
| Adapter PEFT | **15** |
| Merge Hugging Face | **15** |
| GGUF F16 | **15** |
| GGUF Q4_K_M | **14** |

A paridade entre os artefatos foi boa. A competência absoluta permaneceu insuficiente.

Mais importante: **quatro casos de violação de segurança continuaram presentes em todos os braços diagnósticos**.

O pipeline de treinamento e exportação cumpriu uma função. O candidato não atingiu os critérios que justificariam autonomia operacional.

Isso mudou nossa interpretação de um teste verde: converter e carregar pesos corretamente é uma propriedade técnica útil, mas não é prova de desempenho adequado, segurança ou prontidão para produção.

[Experimento LoRA e limites da avaliação](../projects/fine-tuning-evaluation.md).

## 3. Medir velocidade sem medir conclusão produziu uma resposta incompleta

No benchmark local de setembro, avaliamos Qwen3.5-4B, Ministral-3-8B-Instruct e Phi-4-multimodal-instruct com uma pequena carga fixa, registrando latência, memória, conclusão e aspectos de qualidade.

| Modelo | Mediana de decodificação | Respostas textuais concluídas |
| --- | ---: | ---: |
| Qwen3.5-4B | **108,39 tokens/s** | 22/25 |
| Ministral | 77,23 tokens/s | 20/25 (uma pulada) |
| Phi-4 multimodal | 63,54 tokens/s | **25/25** |

O Qwen chegou ao limite de geração nos três casos visuais do ensaio. Um resultado parcialmente útil não virou PASS por conveniência.

A avaliação utilizou backends diferentes para parte da matriz e apenas uma passagem por modelo. Não construímos um ranking universal de modelos com esses dados.

A lição foi menos sobre qual LLM escolher e mais sobre **separar capacidade de gerar, concluir, acertar e operar com segurança**.

[Benchmark de inferência local](../projects/local-llm-benchmarking.md).

## 4. O experimento que colocou o contexto longo em perspectiva

Em outubro, investigamos GPT-OSS 20B MXFP4 em uma tarefa sintética de auditoria de rotas de um grafo.

No experimento G3, configuramos uma janela de 98.304 tokens e executamos uma abordagem monolítica. O modelo consumiu **77.103 tokens de entrada e 16.384 tokens gerados**, mas terminou sem produzir o JSON final solicitado.

Nenhum dos doze casos sintéticos foi retornado como resposta final.

Isso não significa que modelos de contexto longo sejam incapazes. Significa que, naquele teste, **acomodar um volume grande de evidências não resolveu a execução até o fim**.

O contexto era um recurso. A conclusão continuava sendo um problema de arquitetura.

## 5. O G4H concluiu o caso porque redesenhamos o trabalho

Mantivemos o mesmo corpus sintético e alteramos o processo.

O sistema passou a recuperar apenas as linhas relevantes à etapa atual, solicitar propostas limitadas, utilizar um fluxo HIGH para rascunho e MEDIUM para finalização estruturada, verificar cada candidato contra regras independentes e persistir o progresso aceito em checkpoints atômicos.

Uma proposta inválida era rejeitada **sem apagar o que já havia sido validado**. Uma execução interrompida poderia continuar a partir do checkpoint.

| Evidência observada no G4H | Resultado |
| --- | ---: |
| Casos sintéticos corretos contra oráculo independente | **12/12** |
| Campos exatos | **60/60** |
| Transições aceitas e verificadas | 78 |
| Candidatos rejeitados | 28 |
| Rodadas de checkpoint | 22 |
| Chamadas ao modelo | 44 |

Essa foi uma melhoria do **sistema completo**, não uma prova de que o modelo passou a raciocinar sozinho sobre 96K tokens em uma inferência. No G4H, cada chamada recebeu evidência seletiva, não o contexto monolítico anterior.

Houve custo adicional de chamadas, tempo e complexidade de coordenação. A decisão foi priorizar conclusão verificável e capacidade de retomada naquela tarefa, não alegar uma otimização gratuita de velocidade.

[Comparação metodológica G2 → G3 → G4H](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G2_G3_G4H_COMPARISON.md) · [Laboratório público e testes](https://github.com/Ozzurac/ncma-local-agent-lab).

## O que mudou na engenharia do NCMA

Esses resultados reforçaram princípios que hoje orientam nossas investigações.

**Responsabilidade pequena e explícita.** O modelo interpreta ou propõe candidatos, sem se tornar a própria autoridade.

**Conhecimento seletivo.** Recuperamos as evidências necessárias para a etapa, em vez de assumir que colocar tudo no prompt seja sempre melhor.

**Verificação externa.** JSON válido, texto convincente e autoconfiança não demonstram que uma ação foi correta.

**Recuperação de verdade.** O estado persistente registra trabalho validado, não apenas um resumo da conversa.

**Falhas preservadas.** Resultados negativos e truncamentos não são excluídos da contagem para melhorar uma apresentação.

**Gates diferentes para responsabilidades diferentes.** Benchmark offline, teste unitário, integração funcional, autorização operacional e aceitação pelo usuário não significam a mesma coisa.

Nenhuma dessas decisões elimina completamente erros semânticos. Quando não há um oráculo confiável, o estado correto pode ser **desconhecido**.

## O que ainda não demonstramos

O laboratório também estuda copilotos semânticos, experiências de voz e percepção de tela, integração por SDKs e protocolos de agentes. Essas frentes não são automaticamente promovidas a sistemas comprovados por aparecerem em um diagrama ou funcionarem parcialmente em uma demonstração.

O resultado G4H não comprova controle autônomo de sistema operacional, qualidade generalizada em tarefas reais ou segurança contra entradas adversárias.

Estamos descrevendo **o que os testes mediram**, sem transformar hipótese, arquitetura ou intenção em fato.

## Conclusão

O whitepaper original perguntava quais técnicas permitem transformar um modelo em parte de um sistema de IA operacional.

Os testes fizeram a pergunta evoluir:

> **Qual arquitetura permite que uma capacidade probabilística participe de um trabalho que pode ser concluído, verificado, interrompido, retomado e auditado?**

Às vezes, a resposta está em mais treinamento ou dados melhores. Outras vezes, está em menos contexto por chamada, regras de verificação, políticas de autorização e checkpoints.

O objetivo deixou de ser fazer o modelo **parecer certo**.

Passou a ser tornar observável **o que o sistema realmente conseguiu provar**.

---

### Fontes e limites de publicação

- [Registro de evidências](../EVIDENCE.md)
- [Política de publicação](../PUBLICATION.md)
- [Atlas de arquitetura sanitizada](../architecture/README.md)
- [NCMA Local Agent Lab](https://github.com/Ozzurac/ncma-local-agent-lab)

Retrospectiva de pesquisa independente. Os números são históricos, delimitados por amostras e protocolos específicos. Não são promessas de produção ou certificação de autonomia. Não foram divulgados código ou dados confidenciais de empregadores, ativos protegidos, pesos de modelos, credenciais, mídia pessoal ou topologias privadas.

**NCMA Systems · Outubro de 2026**
