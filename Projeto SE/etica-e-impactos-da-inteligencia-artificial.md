# Ética e Impactos da Inteligência Artificial

**Disciplina:** Inteligência Artificial e LFA — UDF  
**Instituição:** Centro Universitário UDF  
**Professora:** Kadidja Valéria  
**Aula de referência:** 17 de novembro de 2026

> Analise uma decisão do seu projeto, discuta quem pode ser afetado e declare como a inteligência artificial foi usada e verificada.

---

## 1. Leitura e vídeo

### 1.1. Artigo científico — agosto de 2026

**Questão de integridade acadêmica**

Ao declarar o uso de inteligência artificial generativa (IAG) na elaboração do projeto, devo garantir que a declaração não seja genérica, como apenas “utilizei IA”, pois isso impede avaliar o processo real de aprendizagem. É necessário assegurar a autoria humana responsável, registrar especificamente qual ferramenta foi utilizada, com qual finalidade, em quais etapas ela apoiou e como os resultados foram verificados e validados. Dessa forma, a ferramenta não substitui o raciocínio avaliado na atividade, mas atua como apoio ao desenvolvimento do trabalho.

### 1.2. Vídeo — junho de 2026

**Registro para o debate**

A gravação externa não foi anexada ao material consultado. Por isso, a minutagem exata do trecho escolhido deve ser preenchida após a seleção do vídeo.

---

## 2. Análise ética do projeto

### 2.1. Descrição geral do projeto

O projeto consiste em um **sistema especialista híbrido voltado à triagem e ao planejamento de pipelines de Ciência de Dados**, desenvolvido para auxiliar analistas de dados e estudantes na preparação inicial de bases tabulares antes da modelagem. Quem pode ser afetado por suas saídas são os próprios analistas que confiam nas recomendações e as equipes ou clientes que dependem da qualidade dos modelos treinados a partir desses pipelines.

A Inteligência Artificial atua em duas frentes distintas. Primeiro, um modelo de **Aprendizado Supervisionado** (`DecisionTreeClassifier`) recebe o percentual de valores nulos e o percentual de desbalanceamento das classes para prever o diagnóstico da base (`pronto_para_treino` ou `requer_tratamento`). Em seguida, essa previsão entra como um fato dinâmico em um **Sistema Especialista**, no qual regras lógicas definidas pela equipe (`R1` a `R4`) cruzam o diagnóstico do modelo com o fato contextual `volume_pequeno` para prescrever a recomendação operacional final. Entre as recomendações estão aplicar imputação com SMOTE, descartar nulos com *undersampling*, usar validação cruzada com modelos simples ou treinar modelos *ensemble*.

### 2.2. Análise dos aspectos éticos

Para cada aspecto, são apresentados uma situação concreta, as pessoas que podem ser afetadas e a medida adotada pela equipe.

| Aspecto | Situação concreta no projeto | Quem pode ser afetado | Medida adotada pela equipe |
| :--- | :--- | :--- | :--- |
| **Privacidade e dados** | O sistema solicita apenas metadados agregados da base (percentual de nulos, percentual de desbalanceamento e indicador booleano de volume pequeno), sem receber registros individuais ou dados pessoais. Contudo, o usuário poderia tentar inserir trechos brutos de datasets sensíveis no ambiente de execução. | Titulares dos dados presentes nas bases originais analisadas pelos usuários e as organizações proprietárias desses dados. | Restringir as entradas do sistema exclusivamente a métricas estatísticas agregadas (minimização de dados) e instruir expressamente na documentação para não submeter arquivos brutos contendo dados pessoais ou sigilosos. |
| **Viés e equidade** | O modelo de Árvore de Decisão foi treinado com apenas 24 registros sintéticos e equilibrados, aprendendo cortes rígidos (`perc_nulos <= 10.90%` e `desbalanceamento <= 71.75%`). Bases de domínios específicos, como saúde ou detecção de fraudes, possuem desbalanceamento natural superior a 71,75% e podem ser rotuladas sistematicamente de forma inadequada. | Analistas e projetos que trabalham com dados de eventos raros ou populações minoritárias, que podem receber recomendações inadequadas de reamostragem. | Documentar as limitações do dataset simulado, incluir alertas automáticos de extrapolação quando os valores fugirem ao intervalo de treino e recomendar que o analista ajuste os limiares conforme a natureza do domínio. |
| **Transparência** | Ao receber apenas uma prescrição direta, como “Aplicar SMOTE”, o usuário poderia tratar a saída como uma verdade absoluta sem saber se a decisão veio do modelo estatístico ou de uma regra humana. | Usuários do sistema, especialmente estudantes e analistas júnior, que podem aplicar técnicas incorretas por não compreenderem o raciocínio por trás da resposta. | Implementar um mecanismo de explicação (rastro de inferência) que exiba explicitamente a classificação gerada pelo modelo de ML, os fatos considerados e qual regra (`R1` a `R4`) foi disparada. |
| **Supervisão e responsabilidade** | Em uma base de produção com ruídos ocultos ou vazamento de dados (*data leakage*), o sistema pode classificar a base como `pronto_para_treino` e sugerir modelagem imediata, induzindo a criação de um modelo falho. | Equipes de desenvolvimento, gestores e usuários finais impactados pelas decisões do modelo de Ciência de Dados resultante. | Estabelecer na interface e no código que toda saída é apenas uma sugestão de apoio à triagem, exigindo revisão e validação técnica de um cientista de dados humano antes de executar alterações definitivas. |
| **Impacto social** | O sistema democratiza o acesso a boas práticas iniciais de engenharia e ciência de dados para iniciantes, reduzindo erros básicos de pipeline. Porém, se tratado como substituto do julgamento técnico, pode gerar pipelines padronizados e ineficientes em problemas críticos. | Estudantes, profissionais em início de carreira e organizações que utilizam modelos preditivos em serviços essenciais. | Posicionar a ferramenta estritamente como instrumento didático e de triagem preliminar, reforçando a necessidade de análise exploratória humana complementar. |

### 2.3. Relação com o exemplo do Orientador UDF

O exemplo apresentado na atividade mostra que um estudante que trabalha pode ter poucas horas disponíveis para estudo e, ainda assim, dominar o conteúdo. Se o modelo der muito peso às horas de estudo, pode recomendar apoio desnecessário. A equipe pode testar casos com desempenho semelhante e disponibilidades diferentes, apresentar a saída como sugestão e indicar quem pode revisá-la.

A mesma preocupação se aplica ao projeto desenvolvido: uma recomendação gerada a partir de métricas isoladas não deve ser considerada uma conclusão definitiva sobre a qualidade de uma base. Por exemplo, uma base com alto desbalanceamento pode representar corretamente um problema de detecção de eventos raros, e não necessariamente um erro que deve ser corrigido por reamostragem. Por isso, a recomendação precisa considerar o contexto dos dados e permanecer sujeita à análise do profissional responsável.

---

## 3. Debate em sala e reflexão individual

### 3.1. Dilema ético do projeto

O principal dilema do sistema está na **definição do nível de autonomia e rigidez das recomendações de pré-processamento**, por exemplo, sugerir o descarte de registros nulos ou a reamostragem automática em bases grandes.

**Escolha A — Automação direta**

Permitir que o sistema aplique ou recomende diretamente a exclusão de linhas e o balanceamento sem alertas de contexto.

**Consequência:** agiliza o fluxo de trabalho, mas pode apagar registros raros relevantes e distorcer a distribuição real dos dados.

**Escolha B — Recomendação explicável com supervisão humana**

Manter o sistema apenas como consultor, exigindo confirmação humana e emitindo alertas quando os parâmetros fugirem da faixa segura de treinamento.

**Consequência:** exige mais tempo e conhecimento crítico do usuário na etapa de triagem, mas evita perda irreversível de dados e erros graves de modelagem.

**Decisão adotada:** a equipe opta pela Escolha B, mantendo o sistema como ferramenta de recomendação explicável, com supervisão humana e alertas de extrapolação.

### 3.2. Ideia do artigo para sustentar a análise

Conforme a discussão apresentada por Lacerda Júnior (2026), apoiada em Lo (2023) e Kasneci *et al.* (2023), a saída de um modelo não deve ser tratada como uma autoridade epistêmica definitiva, mas como um rascunho probabilístico que exige verificação e supervisão humana efetiva (*human-in-the-loop*). Isso contribui para que a decisão permaneça explicável e revisável.

Essa ideia sustenta a escolha de não permitir que o sistema execute recomendações automaticamente. Mesmo quando o modelo apresenta uma classificação, é necessário que uma pessoa avalie o contexto da base e confirme se a recomendação é adequada.

### 3.3. Reflexão individual pós-debate

Após o debate, mantenho a decisão de restringir o sistema à **Escolha B: recomendação explicável com supervisão humana obrigatória e alerta de extrapolação**. O principal argumento que influenciou essa posição é que a eficiência computacional na triagem não compensa o risco de perda de informação ou viés estatístico quando um modelo treinado em uma amostra pequena opera fora de seu domínio. Para verificar se essa medida funciona na prática, aplicarei testes com casos de borda, incluindo valores extremos e fora do intervalo de treinamento, acompanhados de um checklist de auditoria no qual o usuário precisa confirmar que revisou a distribuição dos dados antes de prosseguir.

### 3.4. Questão para o debate — Orientador UDF

**Pergunta:** Se o Orientador UDF recomendar monitoria, em que condições essa informação deveria ser compartilhada com a coordenação? Quem se beneficiaria e quem poderia ser prejudicado?

**Resposta:**

A recomendação de monitoria deve ser compartilhada com a coordenação somente quando houver uma finalidade pedagógica clara, necessidade institucional e transparência sobre o uso da informação. Sempre que possível, devem ser compartilhados apenas os dados estritamente necessários, evitando expor informações pessoais ou transformar uma recomendação automatizada em um rótulo definitivo sobre o estudante.

O estudante e a equipe pedagógica podem se beneficiar quando a informação é utilizada para oferecer apoio adequado. Por outro lado, o estudante pode ser prejudicado se a recomendação for interpretada como prova de baixo desempenho ou falta de dedicação, sem considerar seu contexto e suas reais capacidades. Portanto, a recomendação deve ser tratada como um indicativo para análise humana, e não como uma decisão final.

---

## 4. Declaração de uso de sistemas de IA

A atividade solicita que os dois campos abaixo sejam incluídos ao final da entrega. É importante distinguir a IA incorporada ao funcionamento do projeto da IA utilizada como apoio à elaboração do trabalho.

### 4.1. IA incorporada ao projeto

O projeto utiliza um **modelo de Aprendizado Supervisionado (`DecisionTreeClassifier`, da biblioteca scikit-learn) integrado a um Sistema Especialista baseado em regras (`SE... ENTÃO...`)** para diagnosticar a qualidade inicial de bases de dados tabulares e recomendar estratégias de pré-processamento e modelagem.

O sistema recebe **o percentual de valores nulos, o percentual de desbalanceamento entre classes e o indicador booleano de volume pequeno** e gera **a classificação de prontidão da base (`pronto_para_treino` ou `requer_tratamento`), a recomendação técnica correspondente (`R1` a `R4`) e o rastro explicativo da decisão**.

A equipe verificou seu funcionamento por meio de **divisão entre treino e teste (`train_test_split`), cálculo de acurácia e matriz de confusão, visualização da árvore de decisão e simulação de três casos inéditos**, incluindo testes de variação isolada de fatos e valores fora do domínio.

As limitações identificadas incluem **o uso de um dataset simulado de apenas 24 registros, com fronteiras lineares artificiais, sensibilidade a ruídos reais não representados no treino e incapacidade de avaliar semântica ou vazamento de dados**.

A revisão cabe a **mim (autor do projeto), durante o desenvolvimento e a validação acadêmica, e ao cientista ou analista de dados humano responsável pelo pipeline antes de qualquer aplicação em produção**.

### 4.2. IA utilizada na elaboração do trabalho

Utilizei **Gemini (Google)** para **estruturar a documentação técnica do repositório (`README.md`), auxiliar na organização do código em Python, gerar a base de dados simulada equilibrada e apoiar a redação e revisão desta análise ética**.

Aproveitei **a estruturação das quatro regras de domínio (`R1` a `R4`), o roteiro explicativo dos componentes do sistema especialista e a organização tabular dos aspectos éticos** após **executar e testar todo o código no ambiente Python/Colab, conferir os cortes matemáticos gerados pela árvore de decisão, validar a acurácia e a matriz de confusão e revisar cada resposta para garantir aderência ao projeto implementado**.

Assumo a responsabilidade pelas escolhas, verificações e pelo conteúdo da entrega.

---

## 5. Entrega e avaliação

A entrega deve conter:

- [x] Descrição do projeto.
- [x] Análise dos aspectos éticos.
- [x] Reflexão individual.
- [x] Declaração de uso de IA incorporada ao projeto.
- [x] Declaração de uso de IA na elaboração do trabalho.

A avaliação considerará:

- A relação entre os riscos e o funcionamento do projeto.
- A viabilidade das medidas propostas.
- A qualidade da reflexão.
- A clareza da declaração de uso de IA.

---

## Referências e materiais consultados

- LACERDA JÚNIOR, Francisco Pires de. *Uso ético da inteligência artificial generativa na educação superior — uma revisão integrativa (2023–2026).* Revista Foco, v. 19, n. 8, ago. 2026.
- INSTITUTO DE ESTUDOS AVANÇADOS DA USP. *O Estado da Arte em Ética na Inteligência Artificial.* Vídeo, jun. 2026.

---
