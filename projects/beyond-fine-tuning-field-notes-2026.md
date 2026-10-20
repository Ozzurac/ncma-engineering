# Além do fine-tuning: o que aprendemos construindo sistemas de IA na prática

> **NCMA Engineering / Field Notes 01 — outubro de 2026**  
> Engenharia experimental de IA: adaptação, inferência local, recuperação seletiva, limites de contexto, verificação determinística e recuperação de estado.

## A pergunta mudou

Em setembro, reunimos 34 técnicas e componentes num mapa chamado *Da LLM Crua ao Sistema de IA Operacional*. O documento ajudava a distinguir pesos, adaptação, runtime, RAG, memória, ferramentas e controles. Na prática, o laboratório nos forçou a fazer outra pergunta: **que parte do sistema realmente precisa mudar para a tarefa terminar corretamente, ser auditável e sobreviver a falhas?**

A resposta não foi abandonar fine-tuning. Foi abandonar a ideia de que o treinamento, isoladamente, comprovaria capacidade operacional. No NCMA experimentamos os dois lados: adaptações que entregaram ganhos claros e modelos que continuaram falhando mesmo depois de alterações promissoras.

## 1. Fine-tuning funciona quando sabemos qual erro reduzir

No experimento de adaptação de reconhecimento de fala para português técnico, a escolha do checkpoint não foi feita pela menor training loss. Usamos um conjunto de desenvolvimento de 40 exemplos para selecionar a época e só depois avaliamos um **holdout congelado de 80 exemplos**. A segunda época foi escolhida, apesar da terceira continuar reduzindo o loss.

Nesse recorte, o **WER caiu de 21,831% para 2,1127%** e o acerto exato de entidades subiu de **22/80 para 73/80**. Num conjunto independente de 24 clipes FLEURS pt-BR para anti-regressão, o WER registrado também melhorou de **3,69% para 1,845%**, com transcrições inteiras exatas estáveis em 20/24.

**O aprendizado:** treinamento especializado pode produzir ganhos muito concretos, mas o resultado depende de uma tarefa definida, separação de dados, seleção sem vazamento e métricas que reflitam o uso pretendido. Um holdout pequeno e controlado não certifica transcrição geral, todos os sotaques, microfones ou o produto completo.

Fonte: [Case study de adaptação e avaliação](https://github.com/Ozzurac/ncma-engineering/blob/main/projects/fine-tuning-evaluation.md).

## 2. Conseguir exportar um LoRA não significa ter um agente confiável

Num segundo experimento, treinamos um adapter LoRA supervisionado para o Qwen3.5-4B e acompanhamos o comportamento nas fronteiras de exportação: adapter PEFT, merge em pesos Hugging Face, GGUF F16 e GGUF Q4_K_M.

Na mesma suíte **diagnóstica de desenvolvimento de 48 tarefas**, os resultados foram **0/48 no controle não refinado, 15/48 no adapter, 15/48 no merge/F16 e 14/48 na quantização Q4**. A diferença de uma tarefa no último formato é um sinal útil de paridade aproximada. Mas quatro casos de violação de segurança persistiram em cada braço.

**O aprendizado:** a cadeia de empacotamento pode preservar o comportamento aprendido sem que esse comportamento seja adequado à operação. O gate de exportação passou; segurança e autonomia não. Importante: a suíte era de desenvolvimento, não um holdout travado para certificação.

Fonte: [Fine-tuning, holdout e paridade de exportação](https://github.com/Ozzurac/ncma-engineering/blob/main/projects/fine-tuning-evaluation.md).

## 3. Benchmark sem encerramento da tarefa mede só parte do problema

Na comparação local entre Qwen3.5-4B, Ministral e Phi-4 multimodal, medimos inferência num conjunto delimitado, com 25 tarefas textuais e casos adicionais por modalidade quando suportados. O throughput mediano foi, respectivamente, **108,39**, **77,23** e **63,54 tokens/s**. Mas as saídas de texto concluídas foram **22/25**, **20/25 (com um caso pulado)** e **25/25**.

A tentação é publicar apenas o número maior. O problema é que completar não equivale a acertar: resposta truncada, saída válida porém errada e falha de autorização são categorias diferentes. O estudo usou backends distintos e apenas uma passada por candidato, portanto não autoriza ranking geral de modelos.

**O aprendizado:** taxa de tokens, tempo até o primeiro token, memória, conclusão, correção e segurança são dimensões separadas. Nenhuma delas deve virar substituta das demais.

Fonte: [Benchmark local de LLMs](https://github.com/Ozzurac/ncma-engineering/blob/main/projects/local-llm-benchmarking.md).

## 4. O experimento que mudou a arquitetura: G3 versus G4H

No laboratório com **GPT-OSS 20B MXFP4**, a execução G3 tentou resolver uma auditoria de grafo sintético em uma geração de alto esforço. Havia **98.304 tokens de contexto configurados**; a execução registrou **77.103 tokens de entrada** e **16.384 tokens de saída**, atingiu o limite de geração e **não produziu JSON final**. Não foi uma vitória escondida por uma métrica imperfeita: a tarefa não terminou.

O G4H manteve o mesmo corpus sintético de 12 incidentes, mas redesenhou o fluxo:

```text
Evidência de origem → recuperação seletiva → proposta HIGH delimitada
                   → finalizador MEDIUM → verificador determinístico
                   → [REJEITA: preserva estado] ou [ACEITA: checkpoint atômico]
                   → próxima unidade de trabalho / recuperação após reinício
                   → oráculo sintético independente ao final
```

O resultado do **sistema inteiro** foi **12/12 incidentes** e **60/60 campos** compatíveis com o oráculo, após **78 transições aceitas**, **28 propostas rejeitadas**, **22 rodadas de checkpoint** e **44 chamadas ao modelo**. Os 96K tokens configurados no G4H **não** foram enviados em cada chamada: o recuperador selecionava evidências pequenas e relevantes. O G4H teve custo acumulado e mais chamadas; não é correto descrevê-lo como simplesmente mais rápido.

**O aprendizado:** a capacidade observada emergiu da decomposição, da validação externa, da recuperação seletiva e da persistência de progresso. Isso **não** prova que o modelo passou a raciocinar autonomamente melhor, nem demonstra precisão geral fora do corpus sintético.

Fontes: [G2 → G3 → G4H](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G2_G3_G4H_COMPARISON.md), [sequência G4H](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G4H_SEQUENCE.md) e [repositório reproduzível](https://github.com/Ozzurac/ncma-local-agent-lab).

## 5. A fronteira entre proposta e autoridade

A diferença mais importante não é o nome do modelo. É a separação de responsabilidades: a LLM produz **candidatos**, o controlador decide quais ações são **permitidas** e um verificador independente determina quais resultados estão **comprovados**. Uma saída JSON bem formada não é uma autorização; uma chamada de API bem-sucedida não é prova de efeito; e um erro sem observabilidade não vira automaticamente sucesso após retry.

Nosso padrão passou a priorizar cinco decisões: unidades pequenas de trabalho, evidência de origem recuperável, regras determinísticas para autorizar ou rejeitar, checkpoints idempotentes e uma contabilidade explícita de falhas. Em cenários com acesso a ferramentas, o escopo e as permissões precisam ser implementados fora do texto probabilístico do modelo.

Fonte: [NCMA Engineering — princípios operacionais](https://github.com/Ozzurac/ncma-engineering/blob/main/README.md) e [política pública de alegações](https://github.com/Ozzurac/ncma-engineering/blob/main/PUBLICATION.md).

## Conclusão

Hoje não perguntamos apenas *qual modelo consegue responder?*. Perguntamos **qual arquitetura conclui, verifica, explica e recupera esse trabalho?**

Treinamento, LoRA, quantização e escolha de modelo continuam importantes. Mas a maturidade operacional aparece quando a hipótese sobre o modelo encontra o ambiente real, encontra limites e sobrevive à medição. O progresso do laboratório inclui tanto o ASR que melhorou quanto o fine-tune que não ficou seguro, o benchmark que truncou e o agente que só concluiu após termos redesenhado o fluxo.

Isso não é uma receita universal. É um registro de decisões, resultados positivos, evidência negativa e limites observados em setembro e outubro de 2026.

### Evidências, escopo e publicação

- [NCMA Engineering](https://github.com/Ozzurac/ncma-engineering) mantém os estudos de caso e seu [registro de evidências](https://github.com/Ozzurac/ncma-engineering/blob/main/EVIDENCE.md).
- [NCMA Local Agent Lab](https://github.com/Ozzurac/ncma-local-agent-lab) inclui corpus sintético, oráculo, checkpoints, testes e notas de reprodução.
- [Política de publicação](https://github.com/Ozzurac/ncma-engineering/blob/main/PUBLICATION.md): não publicar credenciais, material privado, registros reais, dados pessoais ou conteúdos de terceiros sem direitos de redistribuição.

**Autoria:** NCMA Engineering / Valter Lourenço Junior. Texto editorial de engenharia aplicada, não certificação acadêmica, ranking geral de modelos nem anúncio de produto pronto.