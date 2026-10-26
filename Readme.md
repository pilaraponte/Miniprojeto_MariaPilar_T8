# Miniprojeto: Análise Exploratória de Dados de Varejo

**Aluna:** Maria Pilar Aponte
**Turma:** T8 (Análise de Dados com Python)
**Entrega:** Módulo 1, Mini-Projeto Avaliativo (Semana 07)

---

## 1. Objetivo

Realizar uma Análise Exploratória de Dados (AED) da base Varejo: diagnosticar a qualidade dos dados, tratá-los com decisões justificadas, calcular estatísticas e agrupamentos e gerar uma base limpa (`varejo_limpo.csv`) com conclusões compreensíveis para quem não conhece Python.

---

## 2. Origem da base

- **Fonte:** Kaggle, [Base Varejo](https://www.kaggle.com/datasets/namespaiva/base-varejo/data) (autor: namespaiva)
- **Arquivo utilizado:** `base_varejo.csv` (separador `;`)
- **Versão:** versão disponível no Kaggle, baixada em 26/10/2026
- **O que cada linha representa:** **um item de compra**. A mesma compra aparece em várias linhas, uma por produto.
- **Tamanho original:** 830.000 linhas e 14 colunas (10 com dados e 4 colunas vazias geradas por `;` extras no cabeçalho)

---

## 3. Colunas utilizadas

| Coluna | Tipo | Significado |
|---|---|---|
| `DATA` | texto | Data da compra (dd/mm/aaaa) |
| `CO_ID` | inteiro | Identificador da compra |
| `CL_ID` | inteiro | Identificador do cliente |
| `CL_GENERO` | texto | Gênero do cliente (F / M) |
| `CL_EC` | inteiro | Estado civil do cliente (código de 1 a 5) |
| `CL_FHL` | inteiro | Número de filhos do cliente |
| `CL_SEG` | texto | Segmento do cliente (A / B / C) |
| `PR_ID` | inteiro | Identificador do produto |
| `PR_CAT` | texto | Categoria do produto |
| `PR_NOME` | texto | Nome do produto |

> Os identificadores (`CO_ID`, `CL_ID`, `PR_ID`) foram usados só para identificar e contar registros, nunca como medida numérica.

---

## 4. Estrutura do repositório

```
Miniprojeto_MariaPilar_T8/
├── README.md
├── Miniprojeto_MariaPilar_T8.ipynb   # notebook com todo o processo
├── base_varejo.csv                   # base original (entrada)
└── varejo_limpo.csv                  # base tratada (saída)
```

---

## 5. Como executar

1. Clone o repositório:
   ```bash
   git clone https://github.com/pilaraponte/Miniprojeto_MariaPilar_T8.git
   cd Miniprojeto_MariaPilar_T8
   ```
2. Confira se o CSV de entrada `base_varejo.csv` está na mesma pasta do notebook. Se não estiver, baixe-o no Kaggle pelo link acima.
3. Crie o ambiente e instale as dependências (Windows/PowerShell):
   ```powershell
   py -m venv .venv
   .venv\Scripts\Activate.ps1
   pip install pandas ipykernel
   ```
4. Abra o notebook no VS Code, selecione o kernel `.venv` e execute todas as células (**Run All**).
   No Google Colab: faça upload do `base_varejo.csv` na aba **Arquivos** e use **Ambiente de execução → Executar tudo**.
5. Ao final, o arquivo `varejo_limpo.csv` é gerado novamente a partir da base original.

Ambiente usado: Python 3.14 e pandas 3.1.

---

## 6. Diagnóstico de qualidade (antes da limpeza)

| Verificação | Resultado encontrado |
|---|---|
| Colunas sem nome (`Unnamed`) | 4 colunas, todas 100% vazias |
| Valores ausentes nas colunas reais | 0 |
| Textos vazios ou só com espaços | 0 |
| Espaços extras e variações de escrita | 0 (rótulos já padronizados em maiúsculas) |
| `#N/D` (erro de fórmula do Excel) | 3.650 linhas em `PR_CAT` e `PR_NOME`, todas do produto 107 |
| Linhas duplicadas | 96.553 (11,6% da base), em 14.566 das 18.471 compras |

---

## 7. Decisões de limpeza

| Problema | Decisão | Justificativa |
|---|---|---|
| 4 colunas `Unnamed` vazias | Removidas | Criadas pelos `;` extras do cabeçalho; não contêm dados |
| Textos | Padronizados (`strip` + maiúsculas) | Já estavam corretos; a padronização garante grupos consistentes |
| `#N/D` em categoria e nome | Substituído por `NÃO IDENTIFICADO` | O item foi comprado, então a linha foi mantida; só o cadastro do produto é desconhecido. O `isna()` não detectava esse valor, porque para o pandas ele é texto |
| 96.553 duplicatas | **Mantidas** | Sem coluna de quantidade, uma linha repetida (mesma compra e mesmo produto) representa mais de uma unidade do item. O padrão aparece em 78,9% das compras, o que indica comportamento de compra, e não erro |

**Antes × depois:** 830.000 registros antes → 830.000 registros depois; colunas de 14 para 10.

---

## 8. Funções criadas

- **`tratar_dados(tabela, colunas_texto, remover_duplicatas=False)`**: recebe a base original e retorna uma cópia tratada (remove colunas vazias, padroniza textos, substitui `#N/D` e, opcionalmente, remove duplicatas).
- **`gerar_resumo(tabela, coluna)`**: recebe uma tabela e o nome de uma coluna numérica e retorna contagem de valores válidos, média, mínimo e máximo.

---

## 9. Análise

### Estatísticas básicas: itens por compra
A base não tem valor monetário nem quantidade, então foi criada a medida **itens por compra** (contagem de linhas por `CO_ID`).
**Unidade analisada:** compra.

| Medida | Valor |
|---|---|
| Contagem de valores válidos | 18.471 compras |
| Média | 44,94 itens |
| Mínimo | 1 item |
| Máximo | 89 itens |

### Filtro aplicado
Compras com mais itens que a média: **9.306 de 18.471 (50,4%)**.

### Agrupamentos
1. **Itens e compras por categoria**: mede quantas unidades de produto (linhas) e quantas compras distintas envolvem cada categoria. ALIMENTOS lidera com 434.767 itens (52,4%), presentes em 98,9% das compras.
2. **Compras por gênero**: mede quantas compras cada gênero fez e o tamanho médio delas. F: 9.615 compras (média de 44,99 itens); M: 8.856 compras (média de 44,88 itens).

Nenhum agrupamento mede faturamento, pois a base não possui valores monetários.

---

## 10. Explicação do processo

A **leitura** dos dados ocorre na Sprint 1 do notebook, com `pd.read_csv("base_varejo.csv", sep=";")`. Sem o `sep=";"`, o arquivo era lido como uma única coluna. A **transformação** acontece na função `tratar_dados()` (Sprint 3), que remove as colunas vazias, padroniza os textos e substitui o `#N/D` por `NÃO IDENTIFICADO`, gerando o `df_limpo` sem alterar o `df_original`. A **gravação** é feita na Sprint 5, com `df_limpo.to_csv("varejo_limpo.csv", index=False)`. A decisão de **manter as duplicatas** afetou diretamente a qualidade da análise: removê-las apagaria 96.553 itens (11,6% da base) e reduziria artificialmente o tamanho das compras. Já a substituição do `#N/D` impediu que um erro do Excel aparecesse como se fosse uma categoria real de produto.

---

## 11. Conclusões

1. A base tem **830.000 itens**, distribuídos em **18.471 compras** de **1.000 clientes**; nenhum registro foi perdido na limpeza.
2. Cada compra tem, em média, **44,94 itens** (de 1 a 89), e **50,4%** das compras ficam acima dessa média.
3. **ALIMENTOS** representa **52,4% dos itens** e aparece em **98,9% das compras**; HIGIENE (18,7%) e LIMPEZA (17,6%) vêm em seguida.
4. **ACESSORIOS** é a categoria menos frequente: 1,8% dos itens, presente em 49,9% das compras.
5. Clientes do gênero F fizeram **52,1% das compras**, mas o tamanho médio da compra é praticamente igual entre os gêneros (44,99 × 44,88 itens).

- **Recomendação:** como ALIMENTOS está em quase todas as compras e ACESSORIOS em só metade, vale testar ações de venda cruzada (exposição de acessórios perto de alimentos, combos) para aumentar a presença dessa categoria. Também é recomendado corrigir na origem o cadastro do produto 107 (`#N/D`).
- **Limitação:** a base não tem valor monetário nem coluna de quantidade. Por isso não é possível medir faturamento, e as linhas repetidas foram interpretadas como unidades a mais do mesmo item, sem confirmação na fonte.

 
 