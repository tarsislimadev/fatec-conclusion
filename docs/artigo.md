# OTIMIZAÇÃO DO ATENDIMENTO E SUPORTE TÉCNICO CORPORATIVO UTILIZANDO ARQUITETURA RAG (RETRIEVAL-AUGMENTED GENERATION) E BANCOS DE DADOS VETORIAIS

Autor: Tarsis Mikael Ventura Dumas de Lima

Orientador(a): a definir

Instituição: Fatec Rio Claro

Curso: Tecnologo em Inteligência Artificial

---

## RESUMO

A rápida expansão de bases de conhecimento e documentações técnicas em ambientes industriais e de TI gera gargalos significativos no atendimento de suporte e na consulta a procedimentos operacionais. Embora os Grandes Modelos de Linguagem (LLMs) apresentem alta capacidade de síntese e compreensão de linguagem natural, seu uso direto em domínios fechados é limitado por alucinações, pela falta de acesso a dados privados e pela dificuldade de rastrear a origem das informações apresentadas. Este trabalho propõe o desenvolvimento e a avaliação de um sistema de suporte técnico inteligente fundamentado na arquitetura RAG (*Retrieval-Augmented Generation*). A solução combina a extração semântica de documentos PDF, a segmentação do conteúdo em unidades pesquisáveis, a vetorização por meio de modelos de *embeddings*, o armazenamento em um banco vetorial ChromaDB e a síntese de respostas citadas utilizando LLMs. Além de responder às dúvidas dos usuários, o sistema deverá indicar os trechos documentais utilizados, favorecendo a conferência humana e a auditabilidade do atendimento. Experimentos de avaliação serão conduzidos utilizando o *framework* RAGAS para mensurar a fidelidade (*faithfulness*), a relevância das respostas e a qualidade do contexto recuperado. Espera-se que a abordagem reduza o tempo de localização de procedimentos e ofereça uma alternativa mais controlável ao uso isolado de modelos generativos em cenários corporativos.

Palavras-chave: Inteligência Artificial; Retrieval-Augmented Generation; Banco de Dados Vetorial; Suporte Técnico; LangChain.

---

## 1. INTRODUÇÃO

Nas últimas décadas, a transformação digital nas indústrias e empresas de tecnologia intensificou a geração de ativos de informação não estruturados em uma escala sem precedentes. A migração de manuais físicos para repositórios digitais, a proliferação de wikis corporativas, a documentação de sistemas legados e a troca constante de informações via e-mails e chats resultaram em volumes massivos de dados. Documentos como manuais de equipamentos complexos, normas de segurança rigorosas e guias de resolução de problemas (*troubleshooting*) tornaram-se essenciais para a continuidade operacional. No entanto, a simples digitalização desses ativos não resolveu o problema da acessibilidade; pelo contrário, a fragmentação da informação em múltiplos formatos e locais — fenômeno frequentemente referido como silos de informação — tornou o acesso rápido e preciso a essas informações por técnicos e operadores um desafio operacional crítico.

Essa fragmentação gera um impacto direto na eficiência produtiva. Quando a informação necessária para resolver um incidente técnico está dispersa ou é difícil de localizar, ocorre a dependência excessiva de "especialistas detentores do conhecimento", criando gargalos onde a resolução de um problema depende da disponibilidade de um único indivíduo. Isso não apenas aumenta o risco operacional, mas também impede a democratização do conhecimento tácito dentro da organização, dificultando o treinamento de novos colaboradores e a padronização dos processos de manutenção.

O surgimento dos Grandes Modelos de Linguagem (LLMs) trouxe novos paradigmas para a interação homem-máquina, permitindo que sistemas computacionais compreendam e gerem texto com fluidez quase humana, processando contextos complexos e sintetizando informações de forma coerente. Contudo, a aplicação de modelos genéricos, como os da família GPT ou Llama, no contexto corporativo enfrenta duas limitações críticas: primeiro, a total ausência de conhecimento sobre documentos privados, proprietários e atualizados que não faziam parte do conjunto de dados de pré-treinamento do modelo; segundo, a tendência intrínseca à geração de informações plausíveis, porém factualmente incorretas, fenômeno conhecido como alucinações. 

Além dessas limitações, a adoção de LLMs em organizações envolve questões de governança, confidencialidade e responsabilidade. Documentos de manutenção podem conter informações proprietárias, credenciais, diagramas de infraestrutura ou instruções cujo acesso deve ser controlado. Dessa forma, uma solução de suporte não deve ser avaliada apenas pela fluidez textual da resposta, mas também pela capacidade de respeitar o escopo documental autorizado, registrar as fontes consultadas e sinalizar quando não houver evidências suficientes para responder. A qualidade do sistema depende, portanto, da integração entre recuperação de informação, geração de linguagem e mecanismos de supervisão.

Em contextos de suporte técnico e manutenção industrial, onde a precisão técnica é mandatória, a alucinação de um único passo procedimental, a inversão de uma polaridade elétrica ou a omissão de um aviso de segurança pode levar a falhas catastróficas, danos materiais irreversíveis ou riscos à integridade física dos operadores. Portanto, a utilização de LLMs "puros" é insuficiente e perigosa para domínios de alta criticidade.

Para mitigar tais limitações, a arquitetura RAG (*Retrieval-Augmented Generation*) destaca-se como uma abordagem robusta ao desacoplar a memória factual do modelo de seu motor de raciocínio linguístico. Em vez de confiar exclusivamente nos pesos sinápticos internos do modelo, o RAG recupera dinamicamente trechos relevantes de uma base de conhecimento externa, validada e atualizada, e os fornece ao modelo como contexto imediato para a geração da resposta. Este trabalho apresenta o desenvolvimento de uma aplicação RAG voltada ao suporte técnico corporativo, visando otimizar a recuperação de informações estratégicas, reduzir drasticamente o tempo de resposta do atendimento e eliminar a incidência de erros operacionais através de respostas fundamentadas em evidências citadas e auditáveis.

A proposta parte da hipótese de que a combinação entre busca semântica e geração condicionada por contexto pode melhorar a eficiência do atendimento sem exigir o treinamento completo de um novo modelo de linguagem. Para verificar essa hipótese, o estudo observará não somente a qualidade linguística das respostas, mas também a correspondência entre pergunta, trechos recuperados e resposta final. Essa perspectiva é importante porque um sistema pode produzir uma resposta bem redigida e, ainda assim, recuperar documentos inadequados ou omitir uma condição relevante do procedimento. Assim, a avaliação será orientada pela cadeia completa de atendimento: consulta, recuperação, composição do contexto, geração e apresentação das fontes.

---

## 2. PROBLEMATIZAÇÃO E JUSTIFICATIVA

O problema central abordado neste estudo reside na ineficiência temporal e na vulnerabilidade a erros durante a consulta a documentações técnicas extensas em ambientes de suporte técnico, manutenção e operação industrial. Atualmente, a maioria das organizações depende de sistemas de busca baseados em palavras-chave (*keyword search*) integrados a leitores de PDF ou wikis corporativas. Essa abordagem é inerentemente limitada, pois obriga o técnico a possuir um conhecimento prévio do vocabulário exato utilizado pelo redator do manual. Se um manual utiliza o termo "instabilidade térmica" e o técnico busca por "superaquecimento", o sistema pode falhar em retornar o documento relevante, apesar de a semântica ser a mesma.

Quando a informação crítica não é localizada com agilidade, o impacto é sentido em diversas dimensões da operação:
1. Aumento do *Mean Time to Repair* (MTTR): O tempo médio para reparo cresce exponencialmente quando o técnico gasta mais tempo procurando "como fazer" do que executando a tarefa.
2. Elevação de Custos Operacionais: A indisponibilidade de equipamentos (*downtime*) em linhas de produção pode gerar prejuízos financeiros massivos por hora.
3. Degradação da Experiência do Usuário: A demora no suporte técnico gera frustração no cliente final e reduz a confiança na infraestrutura de TI da empresa.

Esse cenário também produz efeitos menos visíveis, como a repetição de chamados, a execução de procedimentos diferentes para uma mesma falha e a perda de conhecimento quando profissionais experientes deixam a organização. A dificuldade de encontrar a informação correta pode levar os técnicos a utilizar versões desatualizadas de manuais, fóruns não oficiais ou orientações transmitidas informalmente. Em situações críticas, a ausência de uma resposta rastreável dificulta a análise posterior do incidente e a identificação de oportunidades de melhoria.

O problema de pesquisa pode ser sintetizado pela seguinte questão: em que medida um sistema RAG, alimentado por documentação técnica corporativa e capaz de apresentar fontes, melhora a precisão e a eficiência da consulta quando comparado à busca textual convencional? A pergunta direciona a investigação para dois aspectos complementares. O primeiro é a qualidade da recuperação, isto é, a capacidade de localizar os fragmentos que contêm a informação necessária. O segundo é a qualidade da geração, relacionada à produção de uma resposta objetiva, coerente e fiel ao material recuperado.

### Justificativa Prática e Acadêmica

- Impacto Operacional: A otimização da recuperação de informação reduz a dependência de especialistas seniores para dúvidas triviais, permitindo que a equipe de Nível 1 resolva problemas complexos com maior autonomia.

- Confiabilidade e Segurança: Em ambientes de alta criticidade, a precisão da informação é a única barreira contra acidentes. Um sistema que cita a página exata de um manual de segurança elimina a ambiguidade e reduz o risco de interpretações errôneas de procedimentos perigosos.

- Contribuição Científica: Do ponto de vista acadêmico, este trabalho justifica-se pela necessidade de validar a eficácia de arquiteturas RAG em domínios técnicos específicos. A pesquisa visa analisar quantitativamente como a variação de hiperparâmetros de *chunking* (tamanho do bloco e sobreposição) influencia a métrica de fidelidade (*faithfulness*), preenchendo a lacuna entre a teoria de LLMs e a aplicação prática em engenharia de suporte.

- Governança da Informação: A apresentação das fontes cria uma camada de transparência que permite ao usuário conferir a resposta e ao gestor acompanhar quais documentos estão sendo utilizados. Esse mecanismo também facilita a atualização da base, pois documentos obsoletos podem ser identificados e substituídos sem a necessidade de retreinar o modelo de linguagem.

Do ponto de vista acadêmico, a investigação é relevante porque trata o RAG como um sistema composto, e não apenas como uma técnica de geração de texto. A qualidade final pode ser afetada pela extração do PDF, pelo tamanho dos fragmentos, pelo modelo de *embedding*, pelo número de resultados recuperados e pelas instruções enviadas ao LLM. Analisar essas etapas em conjunto contribui para uma compreensão mais realista dos fatores que influenciam a aplicação de modelos generativos em suporte técnico.

---

## 3. OBJETIVOS

### 3.1 Objetivo Geral

Desenvolver e avaliar um sistema de suporte técnico inteligente baseado em RAG, capaz de responder dúvidas operacionais a partir de documentações internas em formato PDF, fornecendo respostas precisas e auditáveis com citação direta de fontes.

### 3.2 Objetivos Específicos

1. Estruturar um *pipeline* de ingestão e vetorização de documentos não estruturados utilizando LangChain e ChromaDB.

2. Implementar uma interface de conversa (*chat*) iterativa que apresente a resposta e o trecho de origem da documentação.

3. Comparar o desempenho de diferentes estratégias de divisão de texto (*chunking*) em termos de precisão e tempo de resposta.

4. Avaliar a fidelidade e a relevância da solução através do *framework* de métricas RAGAS.

5. Registrar as fontes utilizadas em cada resposta e verificar se a citação apresentada corresponde ao conteúdo recuperado.

6. Identificar limitações relacionadas à qualidade dos documentos, à ambiguidade das perguntas e à ausência de evidências na base, definindo situações em que o sistema deverá recomendar a consulta a um especialista humano.

---

## 4. FUNDAMENTAÇÃO TEÓRICA

### 4.1 Grandes Modelos de Linguagem (LLMs)

Os Grandes Modelos de Linguagem (LLMs) são redes neurais profundas de escala massiva, predominantemente fundamentadas na arquitetura *Transformer*, que revolucionou o processamento de linguagem natural ao introduzir o mecanismo de atenção (*self-attention*). Este mecanismo permite que o modelo processe sequências de dados de forma paralela, atribuindo pesos dinâmicos a diferentes partes de uma frase para capturar dependências semânticas de longo alcance, superando as limitações de memórias sequenciais como as redes LSTM (Vaswani et al., 2017).

Treinados em corpora textuais de escala planetária, esses modelos desenvolvem capacidades emergentes de raciocínio lógico, tradução e síntese. Entretanto, é fundamental compreender que a operação de um LLM é essencialmente probabilística: ele não "consulta" uma base de fatos, mas sim prediz o próximo *token* mais provável com base em padrões estatísticos aprendidos durante o treinamento. Em domínios técnicos especializados, essa natureza gera o risco de alucinações — a geração de instruções que parecem tecnicamente corretas devido à sua fluidez linguística, mas que são factualmente falsas. Em contextos industriais, tal comportamento é inadmissível; a alucinação de um parâmetro de voltagem, a inversão de um passo de travamento de segurança ou a omissão de um EPI pode resultar em danos materiais irreversíveis ou acidentes de trabalho fatais.

O comportamento probabilístico não significa que os LLMs sejam inadequados para aplicações técnicas, mas indica que sua utilização precisa ser condicionada por mecanismos externos de controle. Instruções de sistema, filtros de conteúdo e parâmetros de temperatura podem reduzir alguns comportamentos indesejados, porém não garantem, isoladamente, que a resposta esteja apoiada em uma fonte válida. Por isso, a arquitetura da aplicação deve limitar o espaço de informação disponível ao modelo e oferecer ao usuário meios para verificar a resposta antes de executar um procedimento.

### 4.2 Arquitetura Retrieval-Augmented Generation (RAG)

Proposta por Lewis et al. (2020), a arquitetura RAG (*Retrieval-Augmented Generation*) visa resolver a limitação de conhecimento estático dos LLMs. O sistema opera em duas etapas principais: a recuperação (*retrieval*) e a geração (*generation*). No estágio de recuperação, o sistema utiliza a consulta do usuário para buscar, em uma base de dados externa, os fragmentos de texto mais semanticamente similares ao problema apresentado. No estágio de geração, esses fragmentos são concatenados a um *prompt* de sistema que instrui o LLM a responder a pergunta utilizando exclusivamente as informações fornecidas. Essa abordagem transforma o LLM de um "repositório de fatos" em um "motor de raciocínio sobre contextos", garantindo que a resposta seja fundamentada em dados reais e auditáveis.

Em uma implementação prática, o RAG pode ser dividido em uma etapa de preparação e uma etapa de consulta. Na preparação, os documentos são carregados, limpos, divididos e indexados. Na consulta, a pergunta é transformada em vetor, os fragmentos mais próximos são recuperados e o conjunto selecionado é encaminhado ao gerador. Essa separação permite atualizar a documentação sem alterar os parâmetros do LLM. Também permite observar cada componente individualmente, o que facilita a identificação de falhas: uma resposta incorreta pode resultar de uma recuperação inadequada, de um contexto incompleto ou da interpretação indevida de um contexto correto.

Apesar de reduzir o risco de alucinação, o RAG não garante automaticamente a veracidade das respostas. Se a documentação estiver incompleta, contraditória ou desatualizada, o sistema poderá recuperar e sintetizar informações inadequadas. Da mesma forma, uma pergunta fora do domínio da base pode produzir uma resposta fraca caso o sistema não possua uma política explícita para declarar insuficiência de contexto. Por essa razão, a aplicação proposta deverá instruir o modelo a não inventar procedimentos e a indicar a necessidade de validação humana quando a evidência documental for insuficiente.

### 4.3 Embeddings e Bancos de Dados Vetoriais

A eficácia de um sistema RAG depende intrinsecamente da precisão da busca semântica. Diferente da busca tradicional por palavras-chave, que opera na sintaxe (letras e termos), a busca semântica opera no campo do significado. Esta transição é viabilizada pelos *embeddings*, que são representações matemáticas de fragmentos de texto transformados em vetores densos de alta dimensionalidade dentro de um espaço vetorial. 

Através de modelos de *embedding* sofisticados, a semântica de uma frase é capturada em sua totalidade; por exemplo, "falha no motor" e "problema na propulsão" serão mapeadas para coordenadas geometricamente próximas no espaço vetorial, mesmo que não compartilhem nenhum termo idêntico. A distância entre esses vetores é a medida da similaridade semântica.

Para gerenciar esses vetores em escala, utilizam-se bancos de dados vetoriais, como o ChromaDB. Estes sistemas são otimizados para realizar operações de busca por vizinhos mais próximos (*Approximate Nearest Neighbor Search - ANNS*), permitindo a recuperação de milhões de documentos em milissegundos. A similaridade entre a consulta do usuário e os documentos armazenados é geralmente calculada via similaridade de cosseno, que mede o cosseno do ângulo entre dois vetores: quanto menor o ângulo, maior a similaridade semântica. 

Ao recuperar os *k* trechos mais próximos, o sistema assegura que o contexto enviado ao LLM seja o mais pertinente possível. No entanto, a escolha do valor de *k* e do tamanho dos fragmentos é um ponto crítico de ajuste; contextos excessivamente curtos podem omitir a nuance do procedimento, enquanto contextos excessivamente longos podem induzir o fenômeno "lost in the middle", onde o modelo ignora informações cruciais situadas no centro de prompts extensos.

Outro elemento relevante são os metadados associados a cada fragmento. Informações como nome do arquivo, página, versão do documento, setor responsável e data de atualização podem ser utilizadas tanto para filtrar a busca quanto para construir a citação exibida ao usuário. Em um ambiente corporativo, essa camada é essencial para diferenciar procedimentos semelhantes, priorizar documentos vigentes e preservar a rastreabilidade das decisões. Portanto, a indexação não deve armazenar somente o texto e seu vetor, mas também os atributos necessários para interpretar o resultado recuperado.

---

## 5. METODOLOGIA

A metodologia proposta para o desenvolvimento do sistema de suporte técnico divide-se em três fases principais: a construção do *pipeline* de ingestão de dados, a implementação do motor de inferência RAG e a fase de avaliação quantitativa.

### 5.1 Pipeline de Ingestão e Vetorização

O processo de preparação dos dados inicia-se com a extração de texto de documentos em formato PDF. Para lidar com a natureza não estruturada desses arquivos, será utilizado o *framework* LangChain para a orquestração do fluxo. O texto extraído será submetido a um processo de *chunking* (divisão em fragmentos), utilizando a estratégia de *RecursiveCharacterTextSplitter*. Esta técnica divide o texto em blocos de tamanho fixo com uma sobreposição (*overlap*) controlada, garantindo que o contexto semântico não seja perdido nas bordas dos fragmentos.

Antes da indexação, será realizada uma etapa de inspeção dos documentos. Nessa etapa serão identificados arquivos duplicados, páginas sem texto, caracteres obtidos incorretamente por OCR e documentos que apresentem versões conflitantes. Também serão preservados os metadados de origem, especialmente o número da página, para que a recuperação possa ser explicada ao usuário. Essa preparação é necessária porque erros de extração podem comprometer a busca mesmo quando o modelo de *embedding* e o banco vetorial estão configurados adequadamente.

Serão testadas configurações distintas de tamanho de fragmento e sobreposição. Fragmentos menores tendem a oferecer maior precisão temática, mas podem separar uma instrução de suas condições ou exceções. Fragmentos maiores preservam mais contexto, porém podem introduzir informações irrelevantes e aumentar o custo da geração. A comparação dessas configurações permitirá avaliar o equilíbrio entre completude do contexto, precisão da recuperação, tempo de processamento e quantidade de tokens enviada ao LLM.

Cada fragmento será então convertido em um vetor numérico utilizando um modelo de *embeddings* (como o `text-embedding-3-small` da OpenAI ou modelos equivalentes do HuggingFace). Os vetores resultantes, juntamente com seus metadados (número da página e nome do arquivo), serão armazenados no banco de dados vetorial ChromaDB, permitindo buscas eficientes por similaridade de cosseno.

### 5.2 Implementação do Motor de Inferência

O fluxo de atendimento seguirá a seguinte sequência:

1. Consulta: O usuário insere uma dúvida técnica via interface de chat.

2. Recuperação: A consulta é vetorizada e o sistema recupera os *k* fragmentos mais similares da base do ChromaDB.

3. Aumentação: Os fragmentos recuperados são inseridos em um *prompt* estruturado, que define a persona do modelo como um "Especialista em Suporte Técnico" e instrui a resposta a ser baseada estritamente nos documentos fornecidos.

4. Geração: O LLM gera a resposta final, incluindo a citação explícita da fonte (ex: "Conforme o Manual X, página 12...").

5. Validação da resposta: A interface apresenta a resposta acompanhada dos trechos recuperados e dos respectivos metadados. O usuário poderá comparar a síntese com a fonte original e decidir se a orientação é suficiente ou se deve encaminhar o caso para um especialista.

Para reduzir respostas indevidas, o *prompt* deverá estabelecer regras de comportamento. Entre elas estarão a obrigação de utilizar apenas o contexto fornecido, a proibição de criar valores ou etapas não presentes na documentação, a indicação explícita de ausência de informação e a preservação de advertências de segurança. Essas regras não substituem a revisão humana, mas tornam o comportamento do sistema mais previsível e facilitam a análise dos resultados.

### 5.3 Critérios de Avaliação e Métricas

Para validar a eficácia do sistema, será utilizado o *framework* RAGAS (*RAG Assessment*), que permite a avaliação sem a necessidade de um conjunto de respostas "gabarito" (*ground truth*) extensas. As métricas principais serão:

- Fidelidade (*Faithfulness*): Mede se a resposta gerada é derivada exclusivamente do contexto recuperado, combatendo alucinações.

- Relevância da Resposta (*Answer Relevance*): Avalia se a resposta realmente endereça a dúvida do usuário.

- Relevância do Contexto (*Context Precision*): Verifica se os fragmentos recuperados pelo banco vetorial são, de fato, úteis para a resposta.

A avaliação será conduzida através de um conjunto de testes com perguntas reais de suporte técnico, comparando diferentes tamanhos de *chunks* para identificar a configuração ótima de recuperação.

O conjunto de avaliação será organizado por categorias de dificuldade, como localização direta de um procedimento, comparação entre alternativas, identificação de parâmetros técnicos e perguntas cuja resposta não esteja presente na base. Para cada pergunta serão registrados o contexto recuperado, a resposta gerada, as fontes citadas e o tempo de atendimento. Quando possível, especialistas da área atribuirão uma avaliação complementar sobre correção, completude e segurança da orientação. Essa combinação de métricas automáticas e análise humana é importante porque uma medida numérica isolada pode não capturar uma omissão relevante para a operação.

Além das métricas de qualidade, serão observados indicadores de desempenho, como tempo de ingestão, tempo médio de recuperação, tempo total de resposta e consumo aproximado de tokens. A análise comparará as configurações de *chunking* mantendo constantes, tanto quanto possível, o modelo de *embedding*, o número de documentos e os parâmetros do LLM. Os resultados serão apresentados de forma comparativa, destacando os ganhos, os custos e as situações em que o sistema não deve substituir a decisão de um profissional.

### 5.4 Limitações e Aspectos Éticos

O sistema proposto terá como limite a qualidade e a abrangência da documentação disponibilizada. Um resultado bem avaliado em uma base específica não garante o mesmo desempenho em outros setores, idiomas ou tipos de documento. Além disso, métricas baseadas em modelos de linguagem podem apresentar vieses e devem ser interpretadas em conjunto com avaliações humanas. A investigação também não tratará a resposta automática como autorização para executar intervenções de risco: procedimentos de segurança, manutenção elétrica e operações críticas deverão permanecer sujeitos aos protocolos da organização e à supervisão de profissionais habilitados.

No tratamento dos dados, deverão ser observados os princípios de minimização, controle de acesso e proteção de informações confidenciais. Documentos corporativos não deverão ser enviados a serviços externos sem autorização, e os registros de perguntas e respostas deverão ser armazenados somente quando houver finalidade definida. Esses cuidados são especialmente relevantes em uma aplicação que pode lidar com informações técnicas proprietárias e dados relacionados a incidentes operacionais.

### 5.5 Resultados Esperados

Espera-se que o protótipo demonstre redução no tempo necessário para localizar informações e aumento da relevância dos trechos apresentados ao usuário em comparação com uma busca baseada exclusivamente em palavras-chave. Também se espera identificar uma configuração de fragmentação que preserve o contexto necessário sem produzir *prompts* excessivamente extensos. Como resultado adicional, a pesquisa deverá explicitar os casos em que a arquitetura RAG apresenta limitações, contribuindo para uma adoção mais responsável da tecnologia em ambientes de suporte.

---

## REFERÊNCIAS

- LEWIS, Patrick et al. Retrieval-augmented generation for knowledge-intensive NLP tasks. Advances in Neural Information Processing Systems, v. 33, p. 9459-9474, 2020.

- VASWANI, Ashish et al. Attention is all you need. Advances in Neural Information Processing Systems, v. 30, 2017.
