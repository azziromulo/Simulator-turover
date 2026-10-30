
# Simulator-turover
People Analytics project that uses statistics and machine learning to understand **why employees leave**, **who is likely to leave**, and the **cost of each departure**. It consists of two deliverables: an interactive HTML dashboard and a comprehensive Power BI project, featuring a semantic model in TMDL and 146 DAX measures.
# Raio-X do Turnover · People Analytics

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?logo=python&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-PBIP%20%7C%20TMDL%20%7C%20DAX-F2C811?logo=powerbi&logoColor=black)
![HTML](https://img.shields.io/badge/Dashboard-HTML%20%2B%20ECharts-E34F26?logo=html5&logoColor=white)
![Status](https://img.shields.io/badge/status-conclu%C3%ADdo-0CA30C)

Projeto de **People Analytics** que usa estatística e machine learning para entender **por que os colaboradores saem**, **quem tende a sair** e **quanto custa repor** cada saída. São dois entregáveis: um dashboard HTML interativo e um projeto Power BI completo, com modelo semântico em TMDL e 146 medidas DAX.

> **Sobre os dados:** a análise usa a base **IBM HR Analytics Employee Attrition & Performance**, um conjunto **fictício e público** criado por cientistas de dados da IBM para estudos de dados. São 1.470 colaboradores e 35 variáveis, sem nenhuma pessoa real. Os números demonstram a metodologia e não descrevem nenhuma empresa.

![Visão geral](docs/01_visao_geral.png)

---

## Perguntas de negócio

| # | Pergunta | Principal achado |
|---|---|---|
| 1 | Qual é a taxa geral de turnover? | **16,1%** (237 de 1.470), IC 95% de 14,3% a 18,1% |
| 2 | Quais áreas e cargos perdem mais gente? | Vendas tem 20,6%. Representante de Vendas chega a **39,8%** (2,5× a média) |
| 3 | Quem ganha menos sai mais? | Sim. A renda mediana de quem sai é **38% menor**, e o efeito continua mesmo comparando pessoas do mesmo nível de cargo |
| 4 | Como a satisfação se relaciona com a saída? | O turnover cai a cada degrau de satisfação. Engajamento crítico: **37%**; engajamento muito alto: **8%** |
| 5 | Quais idades e tempos de casa estão em risco? | 18–24 anos com até 1 ano de casa: **60%** de saída |
| 6 | Qual é o perfil de alto risco? | Nível de entrada, sem stock options, solteiro(a), pouca experiência e viagens frequentes |
| + | Quem tende a sair e qual o gap até repor? | **174 saídas esperadas** em 12 meses, ~21 vagas abertas em média, ~$ 3,1 mi de custo |

---

## Simulador de risco de saída

Você ajusta o perfil de um colaborador e o simulador calcula na hora a **probabilidade de saída**, mostra **o que puxa o risco para cima ou para baixo** e estima o **custo de reposição**.

![Simulador](docs/04_simulador.png)

- Regressão logística multivariada com 23 variáveis. Numéricas padronizadas, odds ratios com IC 95% e VIF < 5.
- Validado fora da amostra com validação cruzada estratificada 5×3: **AUC 0,80**. Os 20% de maior risco concentram **55%** das saídas reais.
- Comparado com Random Forest e Gradient Boosting, o modelo interpretável venceu.

---

## Destaques do dashboard

![Perfil de risco](docs/02_perfil_de_risco.png)

- **Filtros que recalculam a estatística no navegador:** IC de Wilson, qui-quadrado, V de Cramér, tendência de Cochran-Armitage, Mann-Whitney, Kaplan-Meier com log-rank e AUC.
- **Funnel plot** para separar os cargos realmente fora da curva da variação natural dos grupos pequenos.
- **Curvas de sobrevivência (Kaplan-Meier)** por nível, estado civil, stock options, viagens e faixa etária.
- **Personas de risco**, geradas por árvore de decisão, e **lista de retenção prioritária**.
- **Gap de reposição:** modelo de remuneração × satisfação × idade × tempo de casa, com dias de vaga aberta, rampa de produtividade, custo de contratação (premissas do SHRM 2025) e cenários de retenção com ROI.

![Gap de reposição](docs/03_gap_de_reposicao.png)

---

## Métodos estatísticos

| Técnica | Para quê |
|---|---|
| IC 95% de Wilson | Intervalo confiável para taxas, inclusive em grupos pequenos |
| Qui-quadrado + V de Cramér | Associação de cada variável com a saída e tamanho do efeito |
| Resíduo ajustado de Haberman | Saber se a categoria difere do restante (\|z\| > 1,96) |
| Cochran-Armitage | Tendência em escalas ordenadas (satisfação, faixas) |
| Mann-Whitney, Welch, d de Cohen | Comparar variáveis numéricas entre quem sai e quem fica |
| Benjamini-Hochberg (FDR) | Correção para 42 testes simultâneos |
| Information Value / WoE | Poder preditivo de cada variável |
| Kaplan-Meier + log-rank | Retenção ao longo do tempo de casa |
| Regressão logística + odds ratios | Efeito de cada fator, controlando os demais |
| Árvore de decisão | Personas de risco legíveis |
| Lei de Little | Vagas abertas em média = saídas por dia × tempo de reposição |

---

## Power BI

- Projeto **PBIP** com modelo semântico em **TMDL** e relatório em **PBIR**: 9 páginas e 101 visuais.
- **146 medidas DAX** organizadas em pastas. Entre elas: IC de Wilson, qui-quadrado e V de Cramér, Kaplan-Meier, AUC calculada em DAX, setas ▲▼ com cor condicional e o modelo de gap.
- **Field parameter** para analisar o turnover por 24 dimensões, parâmetros what-if e premissas por nível de cargo.
- Os JSONs do relatório foram validados contra os schemas oficiais da Microsoft.

![Layout das páginas](docs/powerbi_layout_paginas.png)

---

## Estrutura do repositório

```
├── dashboard/
│   ├── HR_Attrition_Dashboard.html     # dashboard completo (9 abas)
│   └── Simulador_Risco_Saida.html      # simulador standalone
├── powerbi/
│   ├── HR_Attrition.pbip               # abrir no Power BI Desktop
│   ├── HR_Attrition.SemanticModel/     # TMDL: tabelas, medidas, relacionamentos
│   ├── HR_Attrition.Report/            # PBIR: páginas e visuais
│   ├── data/                           # CSVs consumidos pelo modelo
│   └── kit/                            # DAX, Power Query, script TMDL e tema
├── analise/                            # código Python que gera tudo
├── dados/                              # base original
└── docs/                               # documentação completa e imagens
```

## Como rodar

**Dashboard e simulador:** baixe e abra os arquivos de `dashboard/` no navegador. É preciso internet para carregar a biblioteca de gráficos e as fontes.

**Power BI:**
1. Clone ou extraia o repositório em `C:\HR_Attrition_People_Analytics\`. Se usar outra pasta, ajuste o parâmetro `PastaDados` depois de abrir.
2. Abra `powerbi/HR_Attrition.pbip` no Power BI Desktop.
3. Clique em **Atualizar**.

**Reproduzir a análise:**
```bash
pip install -r analise/requirements.txt
python analise/analysis.py dados/WA_Fn-UseC_-HR-Employee-Attrition.csv build
python analise/build_html.py analise/dashboard_template.html build/dashboard_data.json dashboard/HR_Attrition_Dashboard.html
python analise/build_simulador.py build analise/dashboard_template.html dashboard/Simulador_Risco_Saida.html
```

A metodologia completa, as fórmulas com exemplos, o dicionário de dados e as limitações estão em [`docs/DOCUMENTACAO.md`](docs/DOCUMENTACAO.md).

---

## Stack

Python (pandas, scipy, statsmodels, scikit-learn, lifelines) · Power BI (PBIP, TMDL, DAX, Power Query) · HTML/CSS/JavaScript (Apache ECharts)

## Limitações

- A base é fictícia e não tem datas. A curva de Kaplan-Meier usa os anos de casa como aproximação, e o horizonte de 12 meses é uma premissa.
- Associação não é causalidade. Os odds ratios mostram onde olhar, não o efeito garantido de uma ação.
- A métrica de hora extra foi retirada de propósito da análise.

## Autor

**Romulo Azzi**: BI, dados e marketing · MBA em Gestão de Pessoas e Liderança (PUC)
<!-- adicione aqui o link do seu LinkedIn -->

## Fontes

- IBM HR Analytics Employee Attrition & Performance (dataset fictício para estudo, disponível no Kaggle)
- SHRM, *2025 Recruiting Benchmarking Report* (prazo de contratação e custo por contratação)
