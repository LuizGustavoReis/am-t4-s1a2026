# Relatório de Aprendizado de Máquina - Prova 2

**Aluno:** Luiz Gustavo Reis

**Matéria:** Aprendizado de Máquina

**Tema:** Financiamento de Campanhas Eleitorais (TSE) - Eleições SP 2024

---

## 1. O que Fizemos

Pegamos a base de dados de desprezas contratadas dos candidatos de São Paulo nas eleições municipais de 2024. Agrupamos tudo por candidatura para saber quanto cada campanha gastou.

- **Definição do Alvo:** Criamos uma coluna chamada `Custo_Alto`. Se o candidato está entre os 10% que mais gastaram, o valor é 1. Se não, é 0. O valor de corte foi de **R$ 15.000,00**.

- **Atributos Preditores:** Usamos o cargo e o partido do candidato.

- ** One-Hot Encoding:** Mudamos o código para usar One-Hot Encoding, criando variáveis binárias de 0 e 1. Isso foi necessário porque o código anterior dava pesos errados para os partidos e carros.

- **Divisão Treino/Teste:** Dividimos os dados em 85% para treino e 15% para teste.

- **Padronização:** Usamos `StandardScaler` apenas para os modelos de KNN, SVM e Rede Neural, pois eles precisam que as escalas estejam iguais.

---

## 2. Resultados dos Modelos

Abaixo estão as métricas que obtêm no conjunto de teste:

| Modelo | Acurácia | Precisão (Classe 1) | Recall (Classe 1) | F1-Score (Classe 1) |

| :--- | :---: | :---: | :---: | :---: |

| **Árvore de Decisão** | 92,43% | 91,56% | 25,29% | 39,63% |

| **Floresta Aleatória** | 92,44% | 91,86% | 25,29% | 39,66% |

| **SVM** | 92,42% | 92,64% | 24,84% | 39,18% |

| **KNN** | 92,43% | 92,38% | 25,02% | 39,38% |

| **Rede Neural (MLP)** | 92,43% | 92,38% | 25,02% | 39,38% |

| **Naive Bayes** | 17,00% | 10,45% | 98,39% | 18,89% |

*Nota: O SVM rodou com uma amostra menor do treino para não travar a máquina.* *

---

## 3. Explicação dos Resultados

### Por que os 5 modelos deram quase a mesma coisa?

Os modelos (Árvore, RF, SVM, KNN e Rede Neural) empataram com acurácia na passagem de 92% e precisão de 91-92%, mas com um Recall baixo (só ~25%).

Isso aqui tem carga e partido do candidato não são suficientes para prever com 100% de certeza quem vai gastar muito. O modelo só chuta que vai ser `Custo_Alto=1` quando a combinação é muito óbvia. Como ele é conservador para não errar, a Precisão fica alta e o Recall fica baixo.

### Por que o Naive Bayes quebrou?

O Naive Bayes deu uma acurácia pessima de 17% e uma precisão de 10%, antecipando que quase todo mundo ia gastar muito.

Isso aconteceu por causa da codificação One-Hot. O Naive Bayes assume que como variáveis são independentes, mas não One-Hot Encoding, se um candidato é Prefeito, ele não pode ser Vereador. Essa dependência quebrou a lógica do Naive Bayes.

---

## 4. Pipeline do Projeto

Fizemos o design do fluxo no Orange Lab:

1. **Arquivo:** Lê o arquivo CSV.

2 . **Amostrador de Dados:** Separa 85% para treino e 15% para teste.

3 . **Modelos:** Conectamos o treino nos blocos de Árvore de Decisão, Floresta Aleatória, Naive Bayes, KNN, SVM e Rede Neural.

4 . ** Teste & Pontuação:** Receba os modelos e os 15% de teste para calcular as métricas.

5 . **Matrix de Confusão:** Conectado no Test & Score para gerar as tabelas de erros.

---

## 5. Qual Modelo Escolher e Análise Ética

### Escolha do Modelo

A melhor escola é a **Árvore de Decisão**, pois é simples e fácil de entender.

### Justificativa Ética

Usar modelos complexos como Redes Neurais ou SVM no serviço público cria uma “caixa preta". Com a **Árvore de Decisão**, temos um modelo “caixa branca". Qualquer pessoa ou auditor consegue ver as regras de decisão escritas de forma simples. Isso garante um sistema justo, sem perseguições invisíveis criadas por algoritmos.