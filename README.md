# Estudo da Produção de Di-Higgs decaindo em pares de Múons e Quarks-b no Acelerador FCC-hh do CERN

Trabalho de Conclusão de Curso (Graduação em Física) apresentado ao **Maracanã Instituto de Física Armando de Dias Tavares — Universidade do Estado do Rio de Janeiro (UERJ)**.

- **Autor:** Levi Phelipe da Silva Franklin
- **Orientador:** Prof. Dr. Andre Sznajder
- **Ano:** 2025
- **Título original:** *Estudo da produção de Di-Higgs decaindo em pares de Múons e de Quarks-b no acelerador FCC-hh do CERN*

---

##  Sobre o projeto

Este repositório reúne o código, as simulações e a monografia referentes ao estudo do canal de decaimento

```
HH → b b̄ μ⁺ μ⁻
```

no **Futuro Colisor Circular hádron-hádron (FCC-hh)** do CERN, com energia de centro de massa de até **100 TeV**.

A produção de pares de bósons de Higgs (di-Higgs) é uma das principais formas de acessar experimentalmente o **autoacoplamento trilinear do Higgs (λ_HHH)**, um parâmetro fundamental para entender a forma do potencial de Higgs e testar a validade do Modelo Padrão. Como esse processo tem seção de choque extremamente pequena e sofre com fundos abundantes, o trabalho explora tanto **cortes cinemáticos (cutflow)** quanto **aprendizado de máquina (XGBoost)** para maximizar a separação entre sinal e fundo.

O trabalho completo (dissertação em PDF) está disponível em [`Biblioteca Virtual (UERJ)`](https://catalogo-redesirius.uerj.br/Terminalweb), pesquisando pelo título do trabalho.

---

##  Metodologia resumida

1. **Geração de eventos** — Amostras de Monte Carlo geradas com `MadGraph5_aMC@NLO` (nível partônico), decaimentos tratados via `MadSpin`/decaimento direto no MadGraph, e chuveiro de pártons + hadronização com `Pythia8`. Todas as amostras foram geradas a **√s = 100 TeV** (feixes de 50 TeV cada), com **1.000.000 de eventos** por canal.
2. **Seleção de objetos** — Reconstrução de jatos com o algoritmo **anti-k_t** (R = 0,4, via `fastjet`), seleção de múons (PID = ±13) e um procedimento de **b-tagging por vértice secundário** (baseado no deslocamento transversal `Lxy`, número de trilhas e massa invariante do vértice reconstruído a partir das informações de verdade do MC).
3. **Cortes cinemáticos (cutflow)** — Seleção sequencial de eventos com base em `p_T`, `η` e massas invariantes dos sistemas di-múon e di-jato, aproximando a janela de massa do bóson de Higgs (125 GeV).
4. **Normalização de pesos** — Os eventos foram ponderados de acordo com a seção de choque de cada processo (`σ`) e a luminosidade integrada do FCC-hh (**L = 30 ab⁻¹**), via `w_i = σ·L / N_gerados`.
5. **Aprendizado de máquina** — Classificador **XGBoost** (Gradient Boosting) treinado para separar sinal (HH) de fundo (ZZ, tt̄, Drell-Yan/JJμμ, HJJ, ZJJ, JJ, ττ), usando variáveis cinemáticas e de b-tagging.
6. **Avaliação** — Curva ROC / AUC, matriz de confusão, importância de variáveis (F-score) e cálculo da **significância estatística** (`S/√B`) para diferentes cortes na pontuação do classificador.

---

##  Estrutura do repositório

| Arquivo | Descrição |
|---|---|
| [`Geração_madgraph`](./Geração_madgraph) | Comandos/cards do `MadGraph5_aMC@NLO` usados para gerar cada canal de sinal e de fundo (processo, decaimentos, energia de feixe de 50 TeV). |
| [`cutflow.ipynb`](./cutflow.ipynb) | Leitura dos arquivos `.hepmc`, reconstrução de jatos/múons, aplicação dos cortes sequenciais (cutflow) e geração dos histogramas de massa invariante. |
| [`Cortes.ipynb`](./Cortes.ipynb) | Pipeline completo de seleção de eventos: leitura dos `.hepmc` em chunks, reconstrução de jatos (anti-k_t), b-tagging por vértice secundário, construção das variáveis físicas, exportação para CSV e normalização dos pesos por luminosidade. |
| [`XGBoost_training.ipynb`](./XGBoost_training.ipynb) | Treinamento do classificador XGBoost, avaliação (ROC/AUC, matriz de confusão, importância de variáveis) e cálculo da significância estatística do modelo. |

> **Observação:** os arquivos brutos de simulação (`.hepmc`) e os CSVs intermediários gerados pelos notebooks **não** estão incluídos neste repositório por questão de tamanho. Os notebooks referenciam caminhos locais (`/home/lphelipe/...`, `/home/levi/...`) que devem ser ajustados para o seu ambiente antes da execução.

---

##  Canais simulados

| Canal | Decaimento | Papel | σ (pb) |
|---|---|---|---|
| `pp → HH` | H → b b̄ \| H → μ⁺μ⁻ | **Sinal** | 1,9552 × 10⁻⁴ |
| `pp → ZZ` | Z → b b̄ \| Z → μ⁺μ⁻ | Fundo | 4,1527 × 10⁻¹ |
| `pp → JJ μ⁺μ⁻` | — | Fundo | 1,3935 × 10³ |
| `pp → ττ` | — | Fundo | 2,4669 × 10⁴ |
| `pp → JJ` | — | Fundo | 7,6219 × 10⁹ |
| `pp → Z JJ` | Z → μ⁺μ⁻ | Fundo | 2,9880 × 10³ |
| `pp → Z JJ` | Z → b b̄ | Fundo | 1,3099 × 10⁴ |
| `pp → H JJ` | H → μ⁺μ⁻ | Fundo | 6,6213 × 10⁻³ |

---

##  Ferramentas e dependências

- **Geração de eventos:** [MadGraph5_aMC@NLO](https://launchpad.net/mg5amcnlo) (+ MadSpin), [Pythia8](https://pythia.org/)
- **Leitura de eventos:** [`pyhepmc`](https://github.com/scikit-hep/pyhepmc) (formato HepMC)
- **Reconstrução de jatos:** [`fastjet`](http://fastjet.fr/) (algoritmo anti-k_t)
- **Análise de dados:** `pandas`, `numpy`
- **Aprendizado de máquina:** [`xgboost`](https://xgboost.readthedocs.io/), `scikit-learn`, `joblib`
- **Visualização:** `matplotlib`
- **Utilitários:** `psutil`, `tqdm`, `gc`, `os`

Instalação sugerida (ambiente Python):

```bash
pip install pandas numpy pyhepmc fastjet xgboost scikit-learn matplotlib psutil tqdm joblib
```

> `fastjet` e `pyhepmc` costumam exigir instalação via `conda`/`pip` com wheels específicos da plataforma; consulte a documentação oficial de cada pacote caso a instalação via `pip` falhe.

---

##  Como reproduzir a análise

1. **Gerar as amostras de Monte Carlo** seguindo os comandos descritos em [`Geração_madgraph`](./Geração_madgraph) (um processo por bloco, 1.000.000 de eventos cada, com `ebeam1 = ebeam2 = 50000 GeV`).
2. **Rodar `Cortes.ipynb`** (ou o script equivalente descrito na monografia, Apêndice A) para:
   - ler os arquivos `.hepmc` de sinal e fundo;
   - aplicar a seleção de múons, jatos e b-tagging por vértice secundário;
   - exportar os eventos selecionados para CSV;
   - normalizar os pesos dos eventos pela luminosidade (`L = 30 ab⁻¹`).
3. **(Opcional) Rodar `cutflow.ipynb`** para reproduzir a tabela de corte sequencial (cutflow) e os histogramas de massa invariante (dijato e di-múon), com e sem pesos.
4. **Rodar `XGBoost_training.ipynb`** para:
   - treinar o classificador XGBoost sobre os dados de treino;
   - avaliar o modelo (curva ROC, matriz de confusão, importância de variáveis);
   - aplicar o modelo a um conjunto de dados independente e calcular a significância estatística (`S/√B`) para diferentes cortes na pontuação.

> Antes de rodar os notebooks, **ajuste todos os caminhos de arquivo** (`.hepmc`, CSVs, modelo salvo, scaler) para a sua estrutura local de diretórios.

---

##  Principais resultados

- **Cutflow:** após todos os cortes cinemáticos (seleção de múons, massa μ⁺μ⁻, ≥2 jatos, massa do dijato e ≥2 jatos-b), o canal de sinal (HH) retém uma fração muito maior de eventos do que os fundos, especialmente em relação a `ZZ`, `JJ` e `ZJJ → b b̄`, que são praticamente eliminados.
- **XGBoost:** o classificador atingiu **AUC ≈ 0,944** nos dados de treino, com as variáveis relacionadas ao vértice secundário dos jatos (massa e número de trilhas do SV) entre as mais importantes para a separação sinal/fundo.
- **Significância:** cortes mais rígidos na pontuação do classificador (score > 0,6–0,9) elevam substancialmente a significância estatística (`S/√B`), evidenciando o potencial do aprendizado de máquina para a observação do canal `HH → b b̄ μ⁺μ⁻` no FCC-hh.

Os detalhes completos, figuras e discussão dos resultados estão na monografia, Capítulo 7).

---

##  Limitações e trabalhos futuros

- Este é um estudo **a nível de partículas** (geradores), **sem simulação de detector** (resolução, ruído, eficiências de reconstrução) — os resultados representam um cenário otimista.
- Algumas variáveis usadas no treinamento (ex.: informações de vértice deslocado baseadas em verdade do MC) não estão disponíveis dessa forma em dados experimentais reais; o próximo passo natural é substituí-las por um algoritmo de b-tagging mais realista.
- Como próximos passos, propõe-se: (i) passar os eventos por um simulador de detector (ex.: Delphes) para incorporar efeitos experimentais; (ii) refinar os cortes de seleção; (iii) testar outras arquiteturas de aprendizado de máquina/aprendizado profundo.

---

##  Referência

Se este trabalho for útil para você, pode citá-lo como:

> FRANKLIN, Levi Phelipe da Silva. *Estudo da produção de Di-Higgs decaindo em pares de Múons e de Quarks-b no acelerador FCC-hh do CERN.* 2025. 80 f. Trabalho de Conclusão de Curso (Graduação em Física) — Maracanã Instituto de Física Armando de Dias Tavares, Universidade do Estado do Rio de Janeiro, Rio de Janeiro, 2025.

A lista completa de referências bibliográficas (MadGraph5, Pythia8, anti-k_t, XGBoost, artigos do CERN/ATLAS/CMS, etc.) está disponível na monografia.

---

##  Licença

Adicione aqui a licença de sua escolha (ex.: MIT, GPL-3.0) caso deseje tornar o repositório reutilizável por terceiros.

##  Contato

Dúvidas, sugestões ou colaborações são bem-vindas — abra uma *issue* neste repositório.
