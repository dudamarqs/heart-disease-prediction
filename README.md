# Predição de Doença Cardíaca

> 🚧 Em desenvolvimento: fase 3 de 7

Modelo de classificação para prever a presença de doença cardíaca a partir de dados clínicos. Usa o [Heart Disease UCI](https://www.kaggle.com/datasets/redwankarimsony/heart-disease-data): 920 pacientes de 4 hospitais (Cleveland, Hungria, Suíça e VA Long Beach), originalmente publicado no [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/45/heart+disease).

## Principais achados da análise exploratória

O dataset tem armadilhas que fariam um modelo aprender **de qual hospital o paciente veio**, e não **se ele está doente**:

| Achado | Por que é um problema |
|---|---|
| Os 123 pacientes da Suíça têm `chol = 0`, e 93,5% deles estão doentes | Zero não é uma medida: é um marcador de origem. O modelo aprenderia "colesterol zero → doente" |
| `ca` falta em 99% dos pacientes da Hungria e de VA Long Beach, e em 1,6% dos de Cleveland | A ausência do dado identifica o hospital |
| O `id` segue blocos por hospital (1–304 Cleveland, 598–720 Suíça…) | `id > 597` dá 81,7% de doentes, contra 41,0% abaixo disso |
| A proporção de doentes vai de 36% (Hungria) a 93,5% (Suíça) | Qualquer marcador de hospital funciona como atalho para o alvo |

Detalhes em [`01_eda.ipynb`](01_eda.ipynb).

## Roteiro

- [x] **Fase 1:** análise exploratória ([`01_eda.ipynb`](01_eda.ipynb))
- [x] **Fase 2:** preparação dos dados ([`02_preparacao.ipynb`](02_preparacao.ipynb))
- [ ] **Fase 3:** baseline e regressão logística ([`03_baseline.ipynb`](03_baseline.ipynb)), em andamento
- [ ] **Fase 4:** pipeline (`ColumnTransformer`) sem vazamento de dados
- [ ] **Fase 5:** validação cruzada, métricas clínicas e escolha do limiar
- [ ] **Fase 6:** interpretação e teste em um hospital que o modelo nunca viu
- [ ] **Fase 7:** modelo servido por API

## Como rodar

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows (no Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
```

Depois, abra os notebooks no VS Code ou no Jupyter e selecione o kernel `.venv`.
