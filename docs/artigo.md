# OTIMIZAÇÃO DO ATENDIMENTO E SUPORTE TÉCNICO CORPORATIVO UTILIZANDO ARQUITETURA RAG (RETRIEVAL-AUGMENTED GENERATION) E BANCOS DE DADOS VETORIAIS

**Autor:** Tarsis Lima  
**Orientador(a):** [Nome do Orientador]  
**Instituição:** Faculdade de Tecnologia de Rio Claro (Fatec Rio Claro)  
**Curso:** Bacharelado em Inteligência Artificial  

---

## RESUMO

A rápida expansão de bases de conhecimento e documentações técnicas em ambientes industriais e de TI gera gargalos significativos no atendimento de suporte e na consulta a procedimentos operacionais. Embora os Grandes Modelos de Linguagem (LLMs) apresentem alta capacidade de síntese e compreensão de linguagem natural, seu uso direto em domínios fechados é limitado por alucinações e pela falta de acesso a dados privados. Este trabalho propõe o desenvolvimento e a avaliação de um sistema de suporte técnico inteligente fundamentado na arquitetura RAG (*Retrieval-Augmented Generation*). A solução combina a extração semântica de documentos PDF, vetorização por meio de modelos de *embeddings*, armazenamento no banco vetorial ChromaDB e síntese de respostas citadas utilizando LLMs. Experimentos de avaliação serão conduzidos utilizando o *framework* RAGAS para mensurar a fidelidade (*faithfulness*) e a relevância das respostas obtidas.

**Palavras-chave:** Inteligência Artificial; Retrieval-Augmented Generation; Banco de Dados Vetorial; Suporte Técnico; LangChain.

---

## 1. INTRODUÇÃO

Nas últimas décadas, a transformação digital nas indústrias e empresas de tecnologia intensificou a geração de ativos de informação não estruturados, como manuais de equipamentos, normas de segurança e guias de resolução de problemas (*troubleshooting*). No entanto, o acesso rápido e preciso a essas informações por técnicos e operadores continua sendo um desafio operacional significativo.

O surgimento dos Grandes Modelos de Linguagem (LLMs) trouxe novos paradigmas para a interação homem-máquina. Contudo, a aplicação de modelos genéricos no contexto corporativo enfrenta duas limitações críticas: a falta de conhecimento sobre documentos privados não incluídos no pré-treinamento e a tendência à geração de informações plausíveis, porém incorretas (alucinações).

Para mitigar tais limitações, a arquitetura RAG (*Retrieval-Augmented Generation*) destaca-se como uma abordagem eficiente ao desacoplar a memória do modelo de seu motor de raciocínio. Este trabalho apresenta o desenvolvimento de uma aplicação RAG voltada ao suporte técnico, visando otimizar a recuperação de informações estratégicas e mitigar erros em respostas operacionais.

---

## 2. PROBLEMATIZAÇÃO E JUSTIFICATIVA

O problema central abordado neste estudo é o **tempo elevado e a taxa de erro na consulta a documentações técnicas extensas em ambientes de suporte técnico e manutenção**.

### Justificativa Prática e Acadêmica
- **Impacto Operacional:** A demora na localização de procedimentos específicos resulta em aumento da indisponibilidade de sistemas e equipamentos (*downtime*).
- **Confiabilidade:** Em ambientes técnicos ou industriais, orientações incorretas podem ocasionar danos a equipamentos ou riscos de segurança do trabalho.
- **Contribuição Científica:** Avaliar quantitativamente o impacto de diferentes estratégias de *chunking* (divisão de texto) e busca híbrida na precisão do RAG, fornecendo métricas reprodutíveis para a literatura de IA aplicada.

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
Os LLMs são redes neurais profundas treinadas em grandes volumes de texto para prever o próximo *token* e gerar sequências coerentes de linguagem natural (Vaswani et al., 2017). A despeito de sua fluência, modelos autorregressivos não possuem garantia de veracidade factológica em domínios específicos.

### 4.2 Arquitetura Retrieval-Augmented Generation (RAG)
Proposta por Lewis et al. (2020), a arquitetura RAG combina um componente de recuperação de informação (*retriever*) baseado em busca semântica com um componente gerador (*generator*). O *retriever* identifica passagens relevantes em uma base de conhecimento externa e as insere como contexto na provocação (*prompt*) enviada ao gerador.

### 4.3 Embeddings e Bancos de Dados Vetoriais
A busca semântica fundamenta-se na conversão de textos em vetores densos multidimensionais (*embeddings*), onde a proximidade geométrica entre vetores reflete a similaridade de significado. Bancos de dados vetoriais, como o ChromaDB, utilizam algoritmos de busca por vizinhos mais próximos para recuperar rapidamente os trechos com maior similaridade de cosseno em relação à consulta do usuário.

---

## 5. METODOLOGIA (Em desenvolvimento)
*(Esta seção detalhará a arquitetura do sistema, ambiente de testes, conjuntura de dados utilizada e os critérios de avaliação)*

---

## REFERÊNCIAS

- LEWIS, Patrick et al. **Retrieval-augmented generation for knowledge-intensive NLP tasks**. Advances in Neural Information Processing Systems, v. 33, p. 9459-9474, 2020.
- VASWANI, Ashish et al. **Attention is all you need**. Advances in Neural Information Processing Systems, v. 30, 2017.
