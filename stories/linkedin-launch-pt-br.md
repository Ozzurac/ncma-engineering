# LinkedIn launch / PT-BR — Além do Fine-Tuning

> **Status:** editorial draft. Publication is deliberately manual.  
> **Format:** native LinkedIn document carousel (8 slides) + caption.

## Ready-to-edit caption

Passei semanas tentando melhorar modelos locais de IA.

A maior parte dos avanços aconteceu quando comecei a melhorar o **sistema ao redor deles**.

Nos experimentos independentes do laboratório NCMA Systems, encontrei resultados bem diferentes.

Em um projeto de reconhecimento de fala para português técnico, a adaptação reduziu o WER de **21,83% para 2,11%**, em um holdout de 80 exemplos. O fine-tuning cumpriu bem a tarefa delimitada.

Em outro projeto, um LoRA para LLM preservou quase todas as respostas durante merge e quantização. Mas o modelo continuou acertando poucas tarefas e manteve casos problemáticos de segurança.

Treinar funcionou tecnicamente. **Não significou aprovar o sistema.**

Depois veio um experimento com GPT-OSS 20B. Uma abordagem monolítica consumiu mais de 77 mil tokens de entrada, esgotou a geração e não entregou resposta final.

Na versão seguinte, não aumentamos o modelo. Mudamos o processo: busca seletiva, propostas menores, validação independente e checkpoints recuperáveis.

Com a mesma tarefa sintética, o sistema passou a concluir **12/12 casos e 60/60 campos**. O resultado é do conjunto completo, não uma prova de raciocínio autônomo generalizado.

Esses testes mudaram o que eu observo ao desenvolver IA:

• Fine-tuning precisa de objetivo e holdout claro.  
• Tokens/s não medem respostas concluídas.  
• Contexto maior não garante execução melhor.  
• JSON correto não significa decisão correta.  
• A autoridade para executar uma ação não deve morar no próprio modelo.  
• Uma falha reproduzida e registrada é evidência, não algo a esconder.

**A pergunta deixou de ser apenas “qual LLM consegue fazer isso?” e passou a ser “qual arquitetura consegue concluir, verificar e recuperar esse trabalho?”**

Organizei os resultados positivos, os fracassos e os limites de cada teste em um novo artigo da NCMA Engineering.

🔎 Estudo e fontes: **[inserir link da publicação após aprovação do PR]**

#AIEngineering #ArtificialIntelligence #AgenticAI #FineTuning #LLM #MLOps

## Eight-slide map

1. Além do Fine-Tuning — pergunta editorial e as quatro camadas.
2. A pergunta mudou — parâmetros versus resultado verificável.
3. Caso positivo — ASR, WER 21,83% → 2,11% em holdout.
4. Caso incompleto — LoRA, paridade de exportação e quatro violações persistentes.
5. Hipótese refutada — 77.103 tokens de entrada, G3 sem resposta final.
6. Mudança arquitetural — G4H: retrieval, proposta, verificação e checkpoint.
7. Autoridade e governança — modelo propõe, controle autoriza, verificador confere.
8. Síntese — a arquitetura constrói confiabilidade.

## Claims to keep intact

- The ASR result covers one 80-example domain holdout, not universal speech accuracy.
- The 48-case LoRA suite is developmental diagnostics, not a locked release holdout.
- The G4H audit is **synthetic** and depends on a coordinator, not unaided 96K-context GPT-OSS 20B.
- Nothing in this post certifies an autonomous tool-use system or production deployment.
- Do not add employer/client specifics, private infrastructure identifiers, unreleased agent code, personal media or model weights.

## Publication order

Approve the engineering article → approve the portfolio landing page → export the carousel as PDF for LinkedIn document post → paste the caption → replace the URL placeholder with the publicly merged article link → publish manually.
