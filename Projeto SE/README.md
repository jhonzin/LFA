# 🧠 Sistema Especialista Híbrido: Diagnóstico de Qualidade de Dados e Recomendação de Pipeline

**Disciplina:** Inteligência Artificial e Linguagens Formais e Autômatos (LFA)  
**Instituição:** UDF — Centro Universitário  
**Professora:** Kadidja Valéria  
**Tema Escolhido (Aula 03/11/2026):** Mineração e Ciência de Dados  
**Repositório & Material de Consulta:** Avaliação A1 (2026)  

---

## 📌 1. Visão Geral e Arquitetura do Projeto

Este projeto implementa uma **Solução Híbrida de Inteligência Artificial** que combina dois paradigmas clássicos da área:
1. **IA Conexionista / Estatística (Aprendizado de Máquina Supervisionado):** Um classificador de **Árvore de Decisão (`DecisionTreeClassifier`)** treinado com dados rotulados para diagnosticar a qualidade técnica de conjuntos de dados (*datasets*) tabulares.
2. **IA Simbólica (Sistema Especialista Baseado em Regras):** Um **Motor de Inferência** que recebe o diagnóstico do modelo de Machine Learning como um **fato dinâmico**, cruza-o com uma restrição operacional informada pelo usuário e aplica regras explícitas (`SE... ENTÃO...`) para recomendar o pipeline ideal de pré-processamento e modelagem.

### Diagrama do Fluxo Híbrido (Integração)

```text
[ Entradas Numéricas do Dataset ]
  ├── perc_nulos (%)
  └── desbalanceamento_classes (%)
                │
                ▼
┌─────────────────────────────────────────┐
│ FASE 1: APRENDIZADO SUPERVISIONADO      │
│ Modelo: DecisionTreeClassifier          │
│ Saída: status_dataset                   │
│ ("pronto_para_treino" ou                │
│  "requer_tratamento")                   │
└───────────────┬─────────────────────────┘
                │ (Previsão vira FATO na Memória de Trabalho)
                ▼
┌─────────────────────────────────────────┐      ┌──────────────────────────────┐
│ FASE 2: SISTEMA ESPECIALISTA            │◄─────┤ Fato Contextual do Usuário   │
│ Motor de Inferência (Forward Chaining)  │      │ volume_pequeno (True/False)  │
│ Consulta Base de Conhecimento (R1..R4)  │      └──────────────────────────────┘
└───────────────┬─────────────────────────┘
                │
                ▼
[ Saída Explicável ao Cientista de Dados ]
  ├── Regra Dispara (R1, R2, R3 ou R4)
  ├── Recomendação Técnica de Pipeline
  ├── Justificativa Lógica (Rastreabilidade)
  └── Aviso de Governança / Limites de Domínio
```

---

## 📖 2. Guia Teórico para Consulta Rápida (Estudo Inicial)

Esta seção responde de forma rigorosa e didática às questões fundamentais cobradas na **Avaliação A1**, baseadas nos materiais da **IBM**, **Lenovo**, **Scikit-Learn**, **Ronald T. Kneusel** e nas notas de aula da **Profa. Kadidja Valéria**.

### 2.1. O que é Aprendizado de Máquina (*Machine Learning*)?
Segundo o *Guia de Aprendizado de Máquina da IBM*, **Machine Learning** é um subcampo da Inteligência Artificial dedicado ao desenvolvimento de algoritmos que identificam padrões estatísticos em dados históricos para realizar previsões ou tomar decisões sem que um programador precise escrever instruções condicionais explícitas para cada caso possível. Como destaca Ronald T. Kneusel, os modelos de ML não compreendem o contexto semântico do mundo real: eles processam vetores numéricos e ajustam parâmetros matemáticos para mapear entradas em saídas.

### 2.2. O que caracteriza o Aprendizado Supervisionado?
No **Aprendizado Supervisionado**, o algoritmo aprende a partir de um **dataset rotulado** — um conjunto de exemplos históricos em que cada entrada já possui a resposta correta conhecida (o **rótulo**). Durante a fase de treinamento (`.fit()`), o modelo compara suas previsões com os rótulos verdadeiros e ajusta sua estrutura interna para minimizar os erros, permitindo generalizar esse aprendizado para casos inéditos.

### 2.3. Atributos ($X$) vs. Rótulo ($y$)
* **Atributos (*Features* / Variáveis Explicativas — $X$):** São as propriedades mensuráveis que descrevem cada observação do problema. No nosso projeto: `perc_nulos` e `desbalanceamento_classes`.
* **Rótulo (*Label* / Variável Alvo — $y$):** É a categoria ou resposta conhecida que desejamos prever. No nosso projeto: `status_dataset` (`"pronto_para_treino"` ou `"requer_tratamento"`).
* **Regra de Ouro:** O rótulo $y$ **jamais** deve ser incluído dentro da matriz de atributos $X$ durante o treino, pois isso causaria vazamento de dados (*data leakage*), fazendo o modelo apenas copiar a resposta em vez de aprender padrões.

### 2.4. Diferença entre Classificação e Regressão
* **Classificação:** O objetivo é prever uma **classe ou categoria discreta** (rótulos qualitativos finitos).  
  * *Exemplo no projeto:* Prever se um dataset está `"pronto_para_treino"` ou `"requer_tratamento"`.
* **Regressão:** O objetivo é prever um **valor numérico contínuo** dentro de um intervalo real.  
  * *Exemplo:* Prever o tempo exato (em segundos) de treinamento de um modelo ou o valor de venda de um imóvel.

### 2.5. Por que separar os dados em Treino e Teste?
Dividir a base de dados (via `train_test_split`) em **Conjunto de Treino** (ex.: $75\%$) e **Conjunto de Teste** (ex.: $25\%$) serve para medir a **capacidade de generalização** do modelo em dados que ele nunca viu.
* Se avaliarmos o modelo nos mesmos dados usados no treino, corremos o risco de mascarar o **Overfitting (Sobreajuste)** — fenômeno em que o modelo "decora" os exemplos de treino (incluindo ruídos), mas falha diante de novos casos.
* O parâmetro `stratify=y` garante que a proporção entre as classes seja preservada tanto no treino quanto no teste, enquanto `random_state=42` assegura a **reprodutibilidade** científica do experimento.

### 2.6. Como interpretar a Árvore de Decisão e a Matriz de Confusão?
* **Árvore de Decisão (`DecisionTreeClassifier`):** Funciona como um jogo estruturado de perguntas binárias ("sim" ou "não") sobre os atributos para particionar os dados até chegar a um nó folha (decisão final). Em cada nó, observamos:
  * **Condição de corte:** Ex.: $\text{perc\_nulos} \le 10.75$.
  * **Impureza de Gini:** Mede o grau de mistura das classes no nó, dada por:
    $$\text{Gini} = 1 - \sum_{i=1}^{C} p_i^2$$
    onde $p_i$ é a proporção da classe $i$ no nó. $\text{Gini} = 0.0$ indica um **nó puro** (100% dos exemplos pertencem a uma única classe).
  * **`samples`:** Quantidade de exemplos de treino que chegaram àquele nó.
  * **`value`:** Distribuição dos exemplos entre as classes naquele nó.
* **Matriz de Confusão e Acurácia:** A matriz cruza as **classes reais (linhas)** com as **classes previstas (colunas)**, permitindo identificar Verdadeiros Positivos ($VP$), Verdadeiros Negativos ($VN$), Falsos Positivos ($FP$) e Falsos Negativos ($FN$). A acurácia global é calculada por:
  $$\text{Acurácia} = \frac{VP + VN}{VP + VN + FP + FN}$$

---

## 🏛️ 3. Fundamentos de Sistemas Especialistas (IA Simbólica)

Conforme estudado nos slides *Sistemas Especialistas: Conhecimento, regras e inferência* (Profa. Kadidja Valéria) e no *Guia Lenovo*, um **Sistema Especialista (SE)** é um programa de Inteligência Artificial projetado para emular o raciocínio analítico de um especialista humano em um **domínio bem delimitado**.

### 3.1. Tarefas Adequadas para Sistemas Especialistas
1. **Diagnóstico:** Identificar a causa de uma falha ou estado a partir de evidências observáveis (aplicado neste projeto).
2. **Interpretação:** Atribuir significado técnico a dados brutos ou leituras de sensores.
3. **Configuração e Planejamento:** Propor sequências de ações ou arquiteturas respeitando restrições operacionais.

### 3.2. Componentes Principais da Arquitetura
1. **Base de Conhecimento:** Armazena o conhecimento formalizado do domínio na forma de **fatos** e **regras de produção** (`SE <condições> ENTÃO <conclusão>`). Diferente de um banco de dados comum, ela guarda as *relações lógicas* entre os conceitos.
2. **Memória de Trabalho:** Área dinâmica que mantém os fatos específicos da consulta atual (dados informados pelo usuário + previsão do ML) e as conclusões intermediárias deduzidas durante a execução.
3. **Motor de Inferência:** O núcleo lógico que compara os fatos presentes na Memória de Trabalho com as premissas das regras na Base de Conhecimento, seleciona a regra compatível e dispara sua ação.
4. **Interface e Mecanismo de Explicação:** Coleta os dados do usuário, entrega a recomendação e fornece a **trilha de auditoria** (quais fatos e qual regra justificaram a decisão).
5. **Aquisição de Conhecimento:** Processo de extrair heurísticas de especialistas humanos (entrevistas, protocolos, manuais) e formalizá-las sem ambiguidade.

### 3.3. Papéis na Engenharia de Sistemas Especialistas
* **Especialista do Domínio:** Profissional que detém o saber prático e valida as regras (no nosso caso, boas práticas de Ciência de Dados).
* **Engenheiro do Conhecimento:** Profissional que estrutura, formaliza e codifica esse saber na Base de Conhecimento e no Motor de Inferência.
* **Usuário Final:** Quem consulta o sistema informando os fatos do caso concreto e aplica a recomendação.

### 3.4. Estratégias de Inferência (Encadeamento)
* **Encadeamento para Frente (*Forward Chaining* — Dirigido por Dados):**  
  Parte dos **fatos iniciais conhecidos** na Memória de Trabalho, verifica quais regras têm suas condições satisfeitas e avança deduzindo conclusões até atingir a recomendação final.  
  *Exemplo no projeto:* Temos os fatos `status_dataset = "requer_tratamento"` (vindo do ML) e `volume_pequeno = True` (vindo do usuário) $\rightarrow$ o motor dispara a regra **R1** $\rightarrow$ conclui a recomendação de imputação + SMOTE.
* **Encadeamento para Trás (*Backward Chaining* — Dirigido por Objetivos):**  
  Parte de uma **hipótese ou objetivo final** e retrocede para verificar se existem fatos que sustentam essa conclusão, fazendo perguntas ao usuário apenas sobre as condições necessárias.  
  *Simulação didática no nosso domínio:* Se o objetivo fosse testar a hipótese *"Recomendar Validação Cruzada K-Fold (R3)"*, o sistema retrocederia exigindo confirmar dois sub-objetivos: (1) o dataset está `"pronto_para_treino"`? e (2) `volume_pequeno` é `True`?

### 3.5. Verificação, Validação e Tipos de Casos de Teste
* **Verificação (Consistência Lógica):** Garante que a base não possui contradições (mesmas condições levando a ações opostas), ciclos infinitos ou regras inalcançáveis.
* **Validação (Adequação Real):** Garante que as recomendações resolvem corretamente os problemas do domínio.
* **Tipos de Testes:**
  * *Caso Positivo:* Todas as condições de uma regra são satisfeitas e a saída esperada é produzida.
  * *Caso Negativo:* Uma condição necessária é falsa, impedindo o disparo indevido da regra.
  * *Caso Incompleto / Fora de Escopo:* Falta informação ou os valores extrapolam o domínio conhecido, exigindo alerta ou encaminhamento humano.

### 3.6. Comparativo: Sistema Especialista vs. Aprendizado Supervisionado

| Dimensão | Sistema Especialista (IA Simbólica) | Aprendizado Supervisionado (ML) |
| :--- | :--- | :--- |
| **Fonte do Conhecimento** | Conhecimento humano explícito formalizado em regras | Padrões estatísticos extraídos de exemplos rotulados |
| **Mecanismo de Decisão** | Dedução lógica (`SE... ENTÃO...`) via Motor de Inferência | Ajuste matemático de parâmetros (`.fit()`) e predição (`.predict()`) |
| **Explicabilidade** | Alta (caixa-branca): rastreia exatamente qual regra disparou | Média a baixa (depende do algoritmo; árvores são legíveis, redes neurais são caixas-pretas) |
| **Manutenção** | Edição manual de regras e fatos na Base de Conhecimento | Coleta de novos dados, re-rotulagem e retreinamento do modelo |
| **Principais Fragilidades** | Rigidez diante de ambiguidade; dificuldade de capturar conhecimento tácito | Dependência de grande volume de dados limpos; risco de *overfitting* e viés amostral |

---

## 🛠️ 4. Especificação Técnica do Projeto (Ciência de Dados)

### 4.1. Definição do Problema (Requisito 1)
* **Domínio:** Mineração e Ciência de Dados.
* **Público-Alvo:** Analistas e cientistas de dados na etapa de Análise Exploratória de Dados (AED).
* **Entradas do Modelo de ML ($X$):**
  1. `perc_nulos` (`float`, $0.0$ a $100.0$): Percentual médio de células ausentes (`NaN`) no dataset.
  2. `desbalanceamento_classes` (`float`, $50.0$ a $100.0$): Proporção da classe majoritária no atributo alvo ($50\%$ = perfeitamente balanceado; $> 70\%$ = desbalanceado).
* **Rótulo Previsto ($y$):** `status_dataset` (`"pronto_para_treino"` ou `"requer_tratamento"`).
* **Fato Contextual do Usuário:** `volume_pequeno` (`bool`: `True` se o dataset tem menos de $500$ registros; `False` se tem $500$ ou mais).

### 4.2. Base de Conhecimento e Origem das Regras (Requisito 2)

As **4 regras** foram elaboradas com base em heurísticas consolidadas de Engenharia de Recursos (*Feature Engineering*) e Estatística Aplicada:

1. **Regra R1:**  
   * **Condição:** `SE status_dataset == "requer_tratamento" E volume_pequeno == True`  
   * **Ação:** *Aplicar imputação estatística (mediana ou moda) nos valores nulos e técnicas de oversampling (como SMOTE) para equilibrar as classes.*  
   * **Origem / Justificativa Técnica:** Em bases pequenas ($< 500$ linhas), descartar linhas com valores ausentes ou remover registros da classe majoritária (*undersampling*) destruiria o poder estatístico da amostra.
2. **Regra R2:**  
   * **Condição:** `SE status_dataset == "requer_tratamento" E volume_pequeno == False`  
   * **Ação:** *Remover registros incompletos (`dropna`) e aplicar subamostragem (*undersampling*) ou ajuste de pesos (`class_weight="balanced"`).*  
   * **Origem / Justificativa Técnica:** Quando há grande volume de dados disponíveis, excluir registros corrompidos evita a introdução de ruído sintético sem comprometer a representatividade.
3. **Regra R3:**  
   * **Condição:** `SE status_dataset == "pronto_para_treino" E volume_pequeno == True`  
   * **Ação:** *Treinar algoritmos de baixa complexidade (ex.: Árvore de Decisão rasa ou KNN) utilizando Validação Cruzada (K-Fold).*  
   * **Origem / Justificativa Técnica:** Datasets limpos porém pequenos apresentam alto risco de *overfitting* em modelos complexos; a validação cruzada maximiza o aproveitamento dos dados para treino e validação.
4. **Regra R4:**  
   * **Condição:** `SE status_dataset == "pronto_para_treino" E volume_pequeno == False`  
   * **Ação:** *Prosseguir com divisão padrão treino/teste e avaliar modelos ensemble robustos (ex.: Random Forest ou Gradient Boosting).*  
   * **Origem / Justificativa Técnica:** Volume expressivo aliado à alta integridade dos dados permite explorar modelos de maior capacidade preditiva com baixo risco de sobreajuste.

### 4.3. Dataset Rotulado (Requisito 3)
* **Origem:** **Dados simulados (sintéticos)** construídos exclusivamente para fins didáticos neste projeto, representando cenários típicos encontrados durante a etapa de *Data Profiling*.
* **Tamanho:** $24$ registros balanceados ($12$ exemplos de `"pronto_para_treino"` e $12$ exemplos de `"requer_tratamento"`).
* **Critérios de Rotulagem:** Um dataset recebe o rótulo `"pronto_para_treino"` se e somente se apresentar $\text{perc\_nulos} \le 10.0\%$ **E** $\text{desbalanceamento\_classes} \le 70.0\%$. Caso contrário, recebe `"requer_tratamento"`.

---

## 🧪 5. Análise dos Casos de Teste Demonstrados (Requisito 6)

No notebook `Sistema_Especialista_Ciencia_Dados_LFA.ipynb`, executamos **5 cenários** (os 3 casos novos obrigatórios + 1 teste comparativo contrafactual + 1 teste de governança fora de domínio):

| Caso | `perc_nulos` | `desbalanceamento` | `volume_pequeno` | Previsão ML | Regra | Recomendação Gerada |
| :--- | :---: | :---: | :---: | :--- | :---: | :--- |
| **Caso 1** | $28.0\%$ | $88.0\%$ | `True` | `requer_tratamento` | **R1** | Imputação estatística + *Oversampling* (SMOTE) |
| **Caso 2** | $3.5\%$ | $55.0\%$ | `False` | `pronto_para_treino` | **R4** | Divisão treino/teste padrão + Modelos *Ensemble* |
| **Caso 3** | $22.0\%$ | $75.0\%$ | `False` | `requer_tratamento` | **R2** | Remoção de nulos (`dropna`) + Ajuste de pesos |
| **Caso 4 (Comparativo)** | $3.5\%$ | $55.0\%$ | `True` | `pronto_para_treino` | **R3** | Modelos simples (Árvore rasa/KNN) + *K-Fold* |
| **Caso 5 (Governança)** | $65.0\%$ | $95.0\%$ | `True` | `requer_tratamento` | **R1** | Emite **ALERTA** de valores fora do domínio de treino |

---

## ⚠️ 6. Discussão Crítica, Limitações e Ética (Requisito 7)

1. **Amostra Pequena e Ilusão de 100% de Acurácia:** O conjunto de teste possui $6$ exemplos ($25\%$ de $24$) e fronteiras de decisão bem separadas. Atingir $100\%$ de acurácia nesse cenário **não comprova eficácia em produção**; em bases reais, métricas de qualidade possuem zonas cinzentas e ruídos que reduzem a acurácia.
2. **Dependência da Qualidade de Entrada (*Garbage In, Garbage Out*):** Como alerta o material da Lenovo, se o cálculo prévio de `perc_nulos` não identificar nulos disfarçados (como strings `"N/A"`, `"?"` ou valores sentinela `-999`), tanto o modelo de ML quanto o Sistema Especialista tomarão decisões equivocadas.
3. **Rigidez das Regras Booleanas:** O motor de inferência atual trata limiares de forma discreta. Um dataset com $10.1\%$ de nulos e outro com $70\%$ caem na mesma categoria `"requer_tratamento"`. Para capturar gradações ("pouco nulo", "moderadamente nulo"), seria necessário integrar **Lógica Fuzzy** (citada no quadro da aula de 03/11).
4. **Governança, LGPD e Supervisão Humana:** Recomendações automatizadas de exclusão de dados (`dropna` ou *undersampling*) devem ser validadas por um cientista de dados humano para evitar a remoção de subgrupos minoritários importantes (viés algorítmico). Em ambientes reais, a inspeção de datasets deve respeitar a **LGPD**, garantindo anonimização de dados pessoais.

---

## ❓ 7. Respostas às Perguntas de Fechamento da Aula

* **1. Qual parte do programa aprende com os dados?**  
  O classificador `DecisionTreeClassifier` da biblioteca `scikit-learn`, durante a execução do método `modelo.fit(X_treino, y_treino)`, que calcula matematicamente os pontos de corte ótimos nos atributos numéricos.
* **2. Qual parte aplica regras humanas?**  
  A lista `regras` (Base de Conhecimento) e a função `aplicar_regras()` (Motor de Inferência), onde especialistas humanos formalizaram as condicionais `SE... ENTÃO...` e suas justificativas técnicas.
* **3. Por que o fato contextual (`volume_pequeno`) não aparece entre os atributos do modelo de ML neste exemplo?**  
  Porque a responsabilidade do modelo de ML foi delimitada para diagnosticar apenas a *integridade intrínseca* dos dados (nulos e equilíbrio). O volume do dataset funciona como uma **restrição operacional/contextual** que define *qual estratégia de engenharia* é viável, sendo processado diretamente pelas regras do Sistema Especialista.
* **4. O que acontece ao mudar apenas esse fato contextual (mantendo as métricas numéricas)?**  
  Como demonstrado na comparação entre o **Caso 2** e o **Caso 4**, a previsão do modelo de ML permanece idêntica (`"pronto_para_treino"`), mas o Motor de Inferência dispara uma regra diferente (**R4** $\rightarrow$ **R3**), alterando a recomendação de modelos *Ensemble* pesados para algoritmos simples com Validação Cruzada.
* **5. Um resultado de 100% em um teste pequeno é suficiente para uso real?**  
  **Não.** Testes pequenos com separação artificialmente limpa não capturam a variabilidade, os outliers e o *data drift* (mudança na distribuição dos dados ao longo do tempo) presentes em sistemas reais.

---

## 🚀 8. Como Executar no Google Colab

1. Abra o [Google Colab](https://colab.research.google.com/).
2. Clique em **Arquivo $\rightarrow$ Fazer upload de notebook** e selecione `Sistema_Especialista_Ciencia_Dados_LFA.ipynb`.
3. Execute as células sequencialmente de cima para baixo (`Ambiente de execução -> Executar tudo` ou `Ctrl + F9`). O notebook já cria automaticamente o arquivo `dataset_qualidade_dados.csv` no ambiente e exibe todos os gráficos e diagnósticos.

---

## 📚 9. Referências Bibliográficas

* **VALÉRIA, Kadidja.** *Sistemas Especialistas: Conhecimento, regras e inferência*. Slides de aula — Inteligência Artificial e LFA. Brasília: Centro Universitário UDF, 2026.
* **VALÉRIA, Kadidja.** *Aprendizado Supervisionado em Sistemas Especialistas: Da lógica baseada em regras às previsões orientadas a dados*. Notebook `Aprendizado_Maquina_Introducao.ipynb`. Brasília: Centro Universitário UDF, 2026.
* **IBM.** *O que é Aprendizado de Máquina (Machine Learning)?* IBM Think. Disponível em: <https://www.ibm.com/br-pt/think/machine-learning>.
* **IBM.** *O que é Aprendizado Supervisionado?* IBM Think. Disponível em: <https://www.ibm.com/br-pt/think/topics/supervised-learning>.
* **LENOVO.** *Sistemas Especialistas: guia completo (IA)*. Base de Conhecimento Lenovo. Disponível em: <https://www.lenovo.com/pt/pt/knowledgebase/expert-systems-a-comprehensive-guide/>.
* **SCIKIT-LEARN.** *DecisionTreeClassifier & train_test_split Documentation*. Disponível em: <https://scikit-learn.org/stable/>.
* **KNEUSEL, Ronald T.** *Como a Inteligência Artificial Realmente Funciona: De feitiçaria a ciência*. São Paulo: Novatec / No Starch Press.
* **FEIGENBAUM, Edward A.** *EAF's Personal Commentary on Expert Systems*. Stanford University.
* **CLIPS.** *A Tool for Building Expert Systems*. Documentação oficial.