# Predição de Doença Cardíaca

> 🚧 Em desenvolvimento: fases 1 a 4 concluídas, fase 5 a seguir.

Modelo de classificação para prever a presença de doença cardíaca a partir de dados clínicos. Utiliza o [Heart Disease UCI](https://www.kaggle.com/datasets/redwankarimsony/heart-disease-data): 920 pacientes de 4 hospitais (Cleveland, Hungria, Suíça e VA Long Beach), originalmente publicado no [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/45/heart+disease).

O foco do projeto é construir um modelo que aprenda **sinais clínicos**, e não **de qual hospital o paciente veio**. O dataset apresenta possíveis atalhos e vieses relacionados à origem dos dados, documentados na análise exploratória.

## Principais achados da análise exploratória

| Achado | Por que é um problema |
|---|---|
| Os 123 pacientes da Suíça têm `chol = 0`, e 93,5% deles estão doentes | Zero não representa uma medida clínica válida nesse contexto. O modelo poderia aprender uma associação entre colesterol zero e doença. |
| `ca` apresenta ausência elevada em alguns hospitais e apenas 1,6% de valores ausentes em Cleveland | A ausência do dado pode funcionar como indicador da origem hospitalar. |
| O `id` segue blocos por hospital (1–304 Cleveland, 598–720 Suíça…) | `id > 597` apresenta 81,7% de pacientes doentes, contra 41,0% abaixo desse valor. |
| A proporção de doentes varia de 36,2% (Hungria) a 93,5% (Suíça) | Marcadores de hospital podem funcionar como atalhos para prever o alvo, sem aprender padrões clínicos generalizáveis. |

Detalhes em [`01_eda.ipynb`](01_eda.ipynb).

## Decisões de preparação

- **Alvo binário:** `num > 0` representa pacientes com doença cardíaca (55,3% dos pacientes). A classe `num = 4` possui apenas 28 pacientes.
- **Zeros impossíveis viram `NaN`:** `chol = 0` e `trestbps = 0`. A imputação é realizada posteriormente, dentro do pipeline, para evitar vazamento de dados.
- **Converter para `NaN` não resolve a armadilha da Suíça:** a ausência de `chol` continua associada à origem hospitalar.
- **`id` e `dataset` ficam fora das variáveis preditoras:** permanecem no DataFrame para rastreabilidade e análises por hospital.
- **`ca`, `thal` e `slope` são mantidas por enquanto:** na fase 5, o modelo será comparado com e sem essas variáveis.

Detalhes em [`02_preparacao.ipynb`](02_preparacao.ipynb).

## Resultados até agora

| Etapa | Resultado |
|---|---|
| Divisão treino/teste | 80/20 estratificada (736 / 184 pacientes), com aproximadamente 55% de doentes nos dois conjuntos |
| Baseline (`DummyClassifier`) | **55,4% de acurácia**: referência inicial baseada na classe majoritária |
| Diagnóstico da regressão logística | O treinamento direto falhou devido à presença de valores ausentes e variáveis categóricas não codificadas |
| Pipeline de pré-processamento | `ColumnTransformer` com imputação, padronização e One-Hot Encoding |
| Regressão logística | **~81% de acurácia** no conjunto de teste |

O primeiro modelo superou o baseline em aproximadamente 25,6 pontos percentuais. Como a avaliação utiliza uma única divisão treino/teste, o resultado ainda pode variar conforme a composição dos dados. A fase 5 investigará a estabilidade do desempenho por meio de validação cruzada e métricas adicionais.

Detalhes em [`03_baseline.ipynb`](03_baseline.ipynb) e [`04_pipeline.ipynb`](04_pipeline.ipynb).

## Roteiro

- [x] **Fase 1:** análise exploratória (`01_eda.ipynb`)
- [x] **Fase 2:** preparação dos dados (`02_preparacao.ipynb`)
- [x] **Fase 3:** baseline e diagnóstico da regressão logística (`03_baseline.ipynb`)
- [x] **Fase 4:** pipeline de pré-processamento com `ColumnTransformer` e regressão logística, evitando vazamento de dados (`04_pipeline.ipynb`)
- [ ] **Fase 5:** validação cruzada, métricas clínicas e escolha do limiar de classificação
- [ ] **Fase 6:** interpretação do modelo e avaliação em um hospital que ele nunca viu
- [ ] **Fase 7:** disponibilização do modelo por API

## Estrutura

| Arquivo | Conteúdo |
|---|---|
| `data/heart_disease_uci.csv` | Dataset original |
| `data/heart_preparado.csv` | Dados preparados na fase 2 |
| `01_eda.ipynb` | Análise exploratória |
| `02_preparacao.ipynb` | Preparação e limpeza dos dados |
| `03_baseline.ipynb` | Divisão treino/teste, baseline e diagnóstico inicial |
| `04_pipeline.ipynb` | Pré-processamento e treinamento da regressão logística |
| `requirements.txt` | Dependências e versões das bibliotecas |

## Como rodar

Requisitos: Python 3.13.

```bash
python -m venv .venv
.venv\Scripts\activate       # Windows
# No Linux/macOS: source .venv/bin/activate

pip install -r requirements.txt
```

Depois, abra os notebooks no VS Code ou no Jupyter, selecione o kernel `.venv` e execute em ordem, da fase 1 à fase 4.