# Esteira de Aprendizado de Máquina — Cervical Cancer (Risk Factors)

## Descrição do projeto

Este projeto implementa uma esteira completa de aprendizado de máquina em um notebook Python (`esteira_de_aprendizado.ipynb`). O objetivo é **prever o resultado da biópsia** (`Biopsy`: 0 = negativa, 1 = positiva) de pacientes a partir de hábitos, histórico ginecológico, histórico de DSTs e resultados de exames de rastreio.

- **Base de dados:** *Cervical Cancer (Risk Factors)*, UCI Machine Learning Repository (858 pacientes, 36 atributos). O arquivo está em `data/`.
- **Tipo de problema:** classificação binária com classes desbalanceadas (~7% de casos positivos).

### Etapas da esteira

| # | Etapa | O que é feito |
|---|---|---|
| 1 | Conjunto de dados | Leitura do CSV (extraído automaticamente do `.zip`, se necessário); `?` tratado como valor ausente |
| 2 | Estatísticas descritivas | `info`, ausentes, `describe`, assimetria, balanceamento do alvo, histogramas e correlações |
| 3 | Transformações nas **colunas** | Remoção de colunas com mais de 80% de ausentes e de colunas constantes; criação de `Years since first intercourse`; `log1p` em variáveis assimétricas |
| 4 | Transformações nas **linhas** | Remoção de linhas duplicadas e de linhas com mais de 30% das variáveis ausentes |
| 5 | Subconjuntos | Treino (60%), validação (20%) e teste (20%), com divisão estratificada |
| 6 | Treino e avaliação | `Pipeline` (imputação pela mediana + padronização + modelo) para 4 candidatos: Regressão Logística, Random Forest, Extra Trees e Gradient Boosting; escolha pela validação |
| 7 | Matriz de confusão e acurácia | Avaliação única no conjunto de teste, com curvas ROC e Precisão-Recall e importância das variáveis |
| 8 | Predição | Modelo salvo com `joblib`, carregado novamente e usado para classificar novas pacientes |

### Resultados (conjunto de teste, 147 pacientes)

Modelo escolhido: **Extra Trees** (limiar de decisão 0,50).

| Métrica | Valor |
|---|---|
| Acurácia | 95,92% (referência de um modelo que sempre responde "negativa": 93,20%) |
| Recall (sensibilidade) | 0,90 |
| Precisão | 0,64 |
| ROC AUC | 0,92 |

Matriz de confusão: 132 verdadeiros negativos, 5 falsos positivos, 1 falso negativo e 9 verdadeiros positivos.

**Observações importantes**

- Como a classe positiva é rara, a acurácia deve ser lida junto com recall e precisão.
- O teste tem apenas 10 casos positivos, então as métricas variam bastante com poucos erros.
- Os exames `Hinselmann`, `Schiller` e `Citology` são mantidos como entradas. Um experimento no notebook mostra que, sem eles, o ROC AUC cai de cerca de 0,94 para cerca de 0,61 (validação cruzada em treino + validação).
- Projeto acadêmico: o modelo não deve ser usado para diagnóstico.

## Estrutura do repositório

```
.
├── README.md
├── requirements.txt
├── esteira_de_aprendizado.ipynb     # notebook com toda a esteira (já executado)
├── data/
│   ├── cervical_cancer_risk_factors.zip
│   └── risk_factors_cervical_cancer.csv
└── modelo/
    └── modelo_cervical.joblib        # modelo salvo pela esteira
```

## Passo a passo para reproduzir a execução

Requisitos: Python 3.9 ou superior.

1. **Obter os arquivos.** Clone o repositório ou baixe e extraia o `.zip` do projeto e entre na pasta raiz:
   ```bash
   cd cervical_cancer_pipeline
   ```
2. **Criar e ativar um ambiente virtual** (opcional, mas recomendado):
   ```bash
   python -m venv .venv
   # Linux / macOS
   source .venv/bin/activate
   # Windows (PowerShell)
   .venv\Scripts\Activate.ps1
   ```
3. **Instalar as dependências:**
   ```bash
   pip install -r requirements.txt
   ```
4. **Abrir o notebook:**
   ```bash
   jupyter notebook esteira_de_aprendizado.ipynb
   ```
5. **Executar todas as células na ordem**, pelo menu *Run > Run All Cells* (ou *Kernel > Restart & Run All*). O notebook precisa ser executado a partir da raiz do projeto, porque lê os dados em `data/` e grava o modelo em `modelo/`.
6. **Conferir os resultados:** estatísticas descritivas (seção 3), transformações (seções 4 e 5), subconjuntos (seção 6), comparação de modelos (seção 7), matriz de confusão e acurácia (seção 8) e predições (seção 9). Todas as sementes aleatórias estão fixas (`SEED = 42`), então os números devem coincidir com os do README.

### Execução sem interface (opcional)

```bash
jupyter nbconvert --to notebook --execute esteira_de_aprendizado.ipynb --output esteira_de_aprendizado.ipynb
```

### Execução no Google Colab (opcional)

1. Envie para o Colab o notebook e a pasta `data/` (com o `.zip` ou o `.csv`).
2. Mantenha a estrutura `data/` ao lado do notebook e execute *Ambiente de execução > Executar tudo*. As bibliotecas usadas já vêm instaladas no Colab.

## Fonte dos dados

Fernandes, K., Cardoso, J. S., Fernandes, J. *Transfer Learning with Partial Observability Applied to Cervical Cancer Screening.* Iberian Conference on Pattern Recognition and Image Analysis, 2017. Base disponível no UCI Machine Learning Repository.
