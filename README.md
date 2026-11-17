# Predição de Doença Cardíaca

> 🚧 Em desenvolvimento: fases 1 a 3 concluídas, fase 4 a seguir

Modelo de classificação para prever a presença de doença cardíaca a partir de dados clínicos. Usa o [Heart Disease UCI](https://www.kaggle.com/datasets/redwankarimsony/heart-disease-data): 920 pacientes de 4 hospitais (Cleveland, Hungria, Suíça e VA Long Beach), originalmente publicado no [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/45/heart+disease).

O foco do projeto é construir um modelo que aprenda **sinais clínicos**, e não **de qual hospital o paciente veio**: o dataset tem várias armadilhas nesse sentido, documentadas abaixo.

## Principais achados da análise exploratória

| Achado | Por que é um problema |
|---|---|
| Os 123 pacientes da Suíça têm `chol = 0`, e 93,5% deles estão doentes | Zero não é uma medida: é um marcador de origem. O modelo aprenderia "colesterol zero → doente" |
| `ca` falta em 99% dos pacientes da Hungria e de VA Long Beach, e em 1,6% dos de Cleveland | A ausência do dado identifica o hospital |
| O `id` segue blocos por hospital (1–304 Cleveland, 598–720 Suíça…) | `id > 597` dá 81,7% de doentes, contra 41,0% abaixo disso |
| A proporção de doentes vai de 36% (Hungria) a 93,5% (Suíça) | Qualquer marcador de hospital funciona como atalho para o alvo |

Detalhes em [`01_eda.ipynb`](01_eda.ipynb).

## Decisões de preparação

- **Alvo binário:** `num > 0` vira doente (55,3% dos pacientes). A classe `num = 4` tem só 28 pacientes.
- **Zeros impossíveis viram `NaN`:** `chol = 0` e `trestbps = 0`. A imputação fica para depois do split, dentro do pipeline, para evitar vazamento.
- **Converter para `NaN` não resolve a armadilha da Suíça:** a ausência de `chol` continua marcando o hospital.
- **`id` e `dataset` ficam fora das variáveis preditoras.** Continuam no DataFrame para a auditoria por hospital.
- **`ca`, `thal` e `slope` são mantidas por enquanto.** Na fase 5, o modelo será comparado com e sem elas.

Detalhes em [`02_preparacao.ipynb`](02_preparacao.ipynb).

## Resultados até agora

| Etapa | Resultado |
|---|---|
| Divisão treino/teste | 80/20 estratificada (736 / 184 pacientes), com 55% de doentes nos dois conjuntos |
| Baseline (`DummyClassifier`, classe majoritária) | **55,4% de acurácia**: o piso que qualquer modelo precisa superar |
| `LogisticRegression` nos dados crus | Falha: há 7 colunas de texto e 9 colunas com nulos. O diagnóstico virou o plano de pré-processamento da fase 4 |

Detalhes em [`03_baseline.ipynb`](03_baseline.ipynb).

## Roteiro

- [x] **Fase 1:** análise exploratória ([`01_eda.ipynb`](01_eda.ipynb))
- [x] **Fase 2:** preparação dos dados ([`02_preparacao.ipynb`](02_preparacao.ipynb))
- [x] **Fase 3:** baseline e diagnóstico da regressão logística ([`03_baseline.ipynb`](03_baseline.ipynb))
- [ ] **Fase 4:** pipeline (`ColumnTransformer`) sem vazamento de dados
- [ ] **Fase 5:** validação cruzada, métricas clínicas e escolha do limiar
- [ ] **Fase 6:** interpretação e teste em um hospital que o modelo nunca viu
- [ ] **Fase 7:** modelo servido por API

## Estrutura

| Arquivo | Conteúdo |
|---|---|
| `data/heart_disease_uci.csv` | Dataset original |
| `data/heart_preparado.csv` | Saída da fase 2, usada a partir da fase 3 |
| `01_eda.ipynb` … `03_baseline.ipynb` | Um notebook por fase. Cada um lê só o que o anterior salvou |
| `requirements.txt` | Versões fixas das bibliotecas |

## Como rodar

Python 3.13, pandas, scikit-learn, matplotlib e seaborn.

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows (no Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
```

Depois, abra os notebooks no VS Code ou no Jupyter, selecione o kernel `.venv` e rode em ordem.
