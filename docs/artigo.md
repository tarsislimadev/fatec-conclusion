# OTIMIZAÇÃO DO ATENDIMENTO E SUPORTE TÉCNICO CORPORATIVO UTILIZANDO ARQUITETURA RAG (RETRIEVAL-AUGMENTED GENERATION) E BANCOS DE DADOS VETORIAIS

Autor: Tarsis Mikael Ventura Dumas de Lima
Orientador(a): a definir
Instituição: Fatec Rio Claro
Curso: Tecnologo em Inteligência Artificial

---

## RESUMO

A rápida expansão de bases de conhecimento e documentações técnicas em ambientes industriais e de TI gera gargalos significativos no atendimento de suporte e na consulta a procedimentos operacionais. Embora os Grandes Modelos de Linguagem (LLMs) apresentem alta capacidade de síntese e compreensão de linguagem natural, seu uso direto em domínios fechados é limitado por alucinações e pela falta de acesso a dados privados. Este trabalho propõe o desenvolvimento e a avaliação de um sistema de suporte técnico inteligente fundamentado na arquitetura RAG (*Retrieval-Augmented Generation*). A solução combina a extração semântica de documentos PDF, vetorização por meio de modelos de *embeddings*, armazenamento no banco vetorial ChromaDB e síntese de respostas citadas utilizando LLMs. Experimentos de avaliação serão conduzidos utilizando o *framework* RAGAS para mensurar a fidelidade (*faithfulness*) e a relevância das respostas obtidas.

Palavras-chave: Inteligência Artificial; Retrieval-Augmented Generation; Banco de Dados Vetorial; Suporte Técnico; LangChain.

---

## 1. INTRODUÇÃO

Nas últimas décadas, a transformação digital nas indústrias e empresas de tecnologia intensificou a geração de ativos de informação não estruturados. A migração de manuais físicos para repositórios digitais, a proliferação de wikis corporativas e a documentação de sistemas legados resultaram em volumes massivos de dados, como manuais de equipamentos, normas de segurança e guias de resolução de problemas (*troubleshooting*). No entanto, a simples digitalização não resolveu o problema da acessibilidade; pelo contrário, a fragmentação da informação em múltiplos formatos e locais — frequentemente referida como silos de informação — tornou o acesso rápido e preciso a essas informações por técnicos e operadores um desafio operacional significativo. Essa fragmentação impede que o conhecimento tácito de especialistas seja democratizado, forçando a dependência de poucos indivíduos e aumentando o risco de gargalos produtivos.

O surgimento dos Grandes Modelos de Linguagem (LLMs) trouxe novos paradigmas para a interação homem-máquina, permitindo que sistemas computacionais compreendam e gerem texto com fluidez quase humana. Contudo, a aplicação de modelos genéricos no contexto corporativo enfrenta duas limitações críticas: a falta de conhecimento sobre documentos privados e proprietários não incluídos no pré-treinamento do modelo e a tendência à geração de informações plausíveis, porém incorretas, fenômeno conhecido como alucinações. Em contextos de suporte técnico, onde a precisão é mandatória, a alucinação de um passo técnico pode levar a falhas catastróficas.

Para mitigar tais limitações, a arquitetura RAG (*Retrieval-Augmented Generation*) destaca-se como uma abordagem eficiente ao desacoplar a memória do modelo de seu motor de raciocínio. Em vez de confiar apenas nos pesos internos do modelo, o RAG recupera dinamicamente trechos relevantes de uma base de conhecimento externa e os fornece ao modelo como contexto para a geração da resposta. Este trabalho apresenta o desenvolvimento de uma aplicação RAG voltada ao suporte técnico, visando otimizar a recuperação de informações estratégicas, reduzir o tempo de resposta do atendimento e mitigar erros em respostas operacionais através de evidências citadas.

---

## 2. PROBLEMATIZAÇÃO E JUSTIFICATIVA

O problema central abordado neste estudo é o tempo elevado e a taxa de erro na consulta a documentações técnicas extensas em ambientes de suporte técnico e manutenção. Atualmente, a dependência de buscas por palavras-chave (*keyword search*) em PDFs ou wikis obriga o técnico a ter conhecimento prévio do termo exato utilizado no manual, ignorando a semântica do problema. Quando a informação não é encontrada rapidamente, o tempo de resolução de incidentes (*Mean Time to Repair - MTTR*) aumenta, elevando os custos operacionais e a frustração do usuário final.

### Justificativa Prática e Acadêmica
- Impacto Operacional: A demora na localização de procedimentos específicos resulta em aumento da indisponibilidade de sistemas e equipamentos (*downtime*).
- Confiabilidade: Em ambientes técnicos ou industriais, orientações incorretas podem ocasionar danos a equipamentos ou riscos de segurança do trabalho.
- Contribuição Científica: Avaliar quantitativamente o impacto de diferentes estratégias de *chunking* (divisão de texto) e busca híbrida na precisão do RAG, fornecendo métricas reprodutíveis para a literatura de IA aplicada.

---

## 3. OBJETIVOS

### 3.1 Objetivo Geral
Desenvolver e avaliar um sistema de suporte técnico inteligente baseado em RAG, capaz de responder dúvidas operacionais a partir de documentações internas em formato PDF, fornecendo respostas precisas e auditáveis com citação direta de fontes.

### 3.2 Objetivos Específicos
1. Estruturar um *pipeline* de ingestão e vetorização de documentos não estruturados utilizando LangChain e ChromaDB.
2. Implementar uma interface de conversa (*chat*) iterativa que apresente a resposta e o trecho de origem da documentação.
3. Comparar o desempenho de diferentes estratégias de divisão de texto (*chunking*) em termos de precisão e tempo de resposta.
4. Avaliar a fidelidade e a relevância da solução através do *framework* de métricas RAGAS.

---

## 4. FUNDAMENTAÇÃO TEÓRICA

### 4.1 Grandes Modelos de Linguagem (LLMs)
Os Grandes Modelos de Linguagem (LLMs) são redes neurais profundas, predominantemente baseadas na arquitetura *Transformer*, que utiliza mecanismos de atenção (*self-attention*) para processar sequências de dados de forma paralela, capturando dependências de longo alcance no texto (Vaswani et al., 2017). Treinados em volumes massivos de dados textuais para prever o próximo *token* em uma sequência, esses modelos desenvolvem capacidades emergentes de raciocínio lógico e síntese. No entanto, a natureza probabilística desses modelos implica que eles não "consultam" fatos em um banco de dados, mas sim geram a resposta mais provável estatisticamente com base nos pesos ajustados durante o treinamento. Em domínios técnicos especializados, essa característica pode levar a alucinações, onde o modelo gera instruções tecnicamente incorretas com alta confiança. Em contextos industriais, tal comportamento é inaceitável, pois a alucinação de um parâmetro de voltagem ou de um passo de segurança pode resultar em danos materiais irreversíveis ou acidentes de trabalho.

### 4.2 Arquitetura Retrieval-Augmented Generation (RAG)
Proposta por Lewis et al. (2020), a arquitetura RAG (*Retrieval-Augmented Generation*) visa resolver a limitação de conhecimento estático dos LLMs. O sistema opera em duas etapas principais: a recuperação (*retrieval*) e a geração (*generation*). No estágio de recuperação, o sistema utiliza a consulta do usuário para buscar, em uma base de dados externa, os fragmentos de texto mais semanticamente similares ao problema apresentado. No estágio de geração, esses fragmentos são concatenados a um *prompt* de sistema que instrui o LLM a responder a pergunta utilizando exclusivamente as informações fornecidas. Essa abordagem transforma o LLM de um "repositório de fatos" em um "motor de raciocínio sobre contextos", garantindo que a resposta seja fundamentada em dados reais e auditáveis.

### 4.3 Embeddings e Bancos de Dados Vetoriais
A eficácia do RAG depende da precisão da busca semântica, que difere da busca por palavras-chave tradicional por operar no campo do significado e não da sintaxe. Esta é viabilizada pelos *embeddings*, que são representações matemáticas de texto em vetores densos de alta dimensionalidade (espaços vetoriais). Através de modelos de *embedding*, a semântica de uma frase é capturada; por exemplo, "falha no motor" e "problema na propulsão" serão mapeadas para posições próximas no espaço vetorial, mesmo sem compartilhar palavras idênticas.

Bancos de dados vetoriais, como o ChromaDB, são otimizados para armazenar esses vetores e realizar operações de busca por vizinhos mais próximos (*Nearest Neighbor Search*). A similaridade entre a consulta do usuário e os documentos armazenados é geralmente calculada via similaridade de cosseno, que mede o ângulo entre dois vetores: quanto menor o ângulo, maior a similaridade semântica. Ao recuperar os *k* trechos mais próximos, o sistema assegura que o contexto enviado ao LLM seja o mais relevante possível. É importante notar que a seleção de *k* deve ser equilibrada para evitar o fenômeno "lost in the middle", onde o modelo ignora informações situadas no centro de contextos excessivamente extensos.

---

## 5. METODOLOGIA

A metodologia proposta para o desenvolvimento do sistema de suporte técnico divide-se em três fases principais: a construção do *pipeline* de ingestão de dados, a implementação do motor de inferência RAG e a fase de avaliação quantitativa.

### 5.1 Pipeline de Ingestão e Vetorização
O processo de preparação dos dados inicia-se com a extração de texto de documentos em formato PDF. Para lidar com a natureza não estruturada desses arquivos, será utilizado o *framework* LangChain para a orquestração do fluxo. O texto extraído será submetido a um processo de *chunking* (divisão em fragmentos), utilizando a estratégia de *RecursiveCharacterTextSplitter*. Esta técnica divide o texto em blocos de tamanho fixo com uma sobreposição (*overlap*) controlada, garantindo que o contexto semântico não seja perdido nas bordas dos fragmentos.

Cada fragmento será então convertido em um vetor numérico utilizando um modelo de *embeddings* (como o `text-embedding-3-small` da OpenAI ou modelos equivalentes do HuggingFace). Os vetores resultantes, juntamente com seus metadados (número da página e nome do arquivo), serão armazenados no banco de dados vetorial ChromaDB, permitindo buscas eficientes por similaridade de cosseno.

### 5.2 Implementação do Motor de Inferência
O fluxo de atendimento seguirá a seguinte sequência:
1. **Consulta**: O usuário insere uma dúvida técnica via interface de chat.
2. **Recuperação**: A consulta é vetorizada e o sistema recupera os *k* fragmentos mais similares da base do ChromaDB.
3. **Aumentação**: Os fragmentos recuperados são inseridos em um *prompt* estruturado, que define a persona do modelo como um "Especialista em Suporte Técnico" e instrui a resposta a ser baseada estritamente nos documentos fornecidos.
4. **Geração**: O LLM gera a resposta final, incluindo a citação explícita da fonte (ex: "Conforme o Manual X, página 12...").

### 5.3 Critérios de Avaliação e Métricas
Para validar a eficácia do sistema, será utilizado o *framework* RAGAS (*RAG Assessment*), que permite a avaliação sem a necessidade de um conjunto de respostas "gabarito" (*ground truth*) extensas. As métricas principais serão:
- **Fidelidade (*Faithfulness*)**: Mede se a resposta gerada é derivada exclusivamente do contexto recuperado, combatendo alucinações.
- **Relevância da Resposta (*Answer Relevance*)**: Avalia se a resposta realmente endereça a dúvida do usuário.
- **Relevância do Contexto (*Context Precision*)**: Verifica se os fragmentos recuperados pelo banco vetorial são, de fato, úteis para a resposta.

A avaliação será conduzida através de um conjunto de testes com perguntas reais de suporte técnico, comparando diferentes tamanhos de *chunks* para identificar a configuração ótima de recuperação.

---

## REFERÊNCIAS

- LEWIS, Patrick et al. Retrieval-augmented generation for knowledge-intensive NLP tasks. Advances in Neural Information Processing Systems, v. 33, p. 9459-9474, 2020.
- VASWANI, Ashish et al. Attention is all you need. Advances in Neural Information Processing Systems, v. 30, 2017.
