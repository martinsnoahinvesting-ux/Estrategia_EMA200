# 📈 Backtest B3 — Estratégia EMA200 + CDB CDI

Simule uma carteira de ações brasileiras com aporte mensal, compra disciplinada pela Média Móvel Exponencial de 200 períodos, caixa remunerado em CDB e reinvestimento automático de proventos — tudo com dados reais da B3, BCB e Yahoo Finance.

> Desenvolvido para o canal **Monte Carlo Multimidia** no YouTube.  
> 100% executável no **Google Colab**, sem instalação local.

---

## 🚀 Como usar em 3 passos

### 1. Abra no Google Colab


> **Dica:** faça uma cópia para o seu Google Drive antes de editar  
> (`Arquivo → Salvar uma cópia no Drive`)

---

### 2. Configure sua carteira (Célula 2)

Essa é a **única célula que você precisa editar**. Altere os parâmetros conforme sua estratégia:

```python
# Ativos que compõem a carteira (sufixo .SA para ações da B3)
TICKERS = ["ITSA4.SA", "TAEE11.SA", "BBAS3.SA", "VULC3.SA"]

# Período do backtest (padrão: últimos 10 anos a partir de hoje)
DATA_INICIO = "2014-04-22"   # ou deixe dinâmico (veja abaixo)
DATA_FIM    = "2024-04-22"

# Aporte mensal total em R$
APORTE_MENSAL = 1000.0

# Percentual do CDI que o CDB rende (1.00 = 100% do CDI)
TAXA_CDB = 1.00

# Fração do caixa comprada por dia quando abaixo da MME200
# 1/20 = ~1 mês de pregões para esvaziar o caixa gradualmente
DEPLOY_DIARIO = 1 / 20
```

**Como encontrar o ticker correto?**

| Ação | Ticker no código |
|------|-----------------|
| Itaúsa | `ITSA4.SA` |
| Taesa | `TAEE11.SA` |
| Banco do Brasil | `BBAS3.SA` |
| Vulcabras | `VULC3.SA` |
| Petrobras PN | `PETR4.SA` |
| Vale | `VALE3.SA` |
| WEG | `WEGE3.SA` |

> Acesse [finance.yahoo.com](https://finance.yahoo.com) e busque o nome da empresa — o ticker aparece com `.SA` no final para ações da B3.

---

### 3. Execute todas as células em ordem

No menu do Colab: **`Ambiente de execução → Executar tudo`** (`Ctrl + F9`)

O notebook roda automaticamente todas as 12 células na sequência correta.

---

## 🧠 Como funciona a estratégia

A estratégia combina 5 regras simples que eliminam a emoção das decisões de investimento:

```
Todo mês → Aporte entra na carteira
              │
              ▼
     Divide igualmente entre os ativos
              │
    ┌─────────┴─────────┐
    │                   │
 Preço <           Preço ≥
 MME200?          MME200?
    │                   │
    ▼                   ▼
Compra aos          Fica no
poucos (DCA)     CDB 100% CDI
    │                   │
    └────────┬──────────┘
             │
     Proventos recebidos?
             │
             ▼
    Reinveste pelas mesmas regras
```

| Regra | Descrição |
|-------|-----------|
| **1. Aporte mensal** | O valor entra no 1º pregão de cada mês |
| **2. Divisão igual** | Cada ativo recebe a mesma fatia do aporte |
| **3. Compra abaixo da MME200** | Só compra quando o preço está em tendência de baixa |
| **4. CDB enquanto espera** | Dinheiro reservado para ações acima da MME200 rende CDI todos os dias |
| **5. Proventos reinvestidos** | Dividendos e JCP voltam para o caixa e seguem as mesmas regras |

---

## 📊 O que o backtest calcula

### Métricas de desempenho (Célula 7)

| Métrica | O que significa |
|---------|----------------|
| **Patrimônio final** | Valor total da carteira no último dia |
| **CAGR** | Taxa de crescimento anual composta |
| **Retorno total** | Lucro percentual sobre o total aportado |
| **Drawdown máximo** | Maior queda acumulada em relação ao topo |
| **Volatilidade anual** | Desvio padrão dos retornos diários, anualizado |
| **Índice de Sharpe** | Retorno ajustado ao risco (CDI como taxa livre) |
| **Alpha vs CDI** | Quanto a carteira superou o CDB ao ano |
| **Proventos reinvestidos** | Total de dividendos + JCP recebidos no período |

### Benchmarks comparados

O backtest compara a carteira contra três estratégias passivas com o mesmo aporte mensal:

| Benchmark | Lógica |
|-----------|--------|
| **CDB 100% CDI** | Todo aporte vai direto para o CDB, rentabilidade diária pelo CDI real do BCB |
| **SELIC** | Mesmo modelo, mas usando a taxa SELIC diária (série 11 do BCB) |
| **IBOVESPA (DCA)** | Compra cotas do índice `^BVSP` no 1º pregão de cada mês |

---

## 📁 Estrutura do notebook

O notebook é dividido em 12 células, cada uma com uma responsabilidade clara:

| Célula | Nome | O que faz |
|--------|------|-----------|
| 1 | Instalação | Instala as dependências com `pip` |
| 2 | ⚙️ **Configurações** | **Edite aqui:** tickers, período, aporte, CDB |
| 3 | Cotações | Baixa preços e dividendos via yfinance |
| 4 | CDI + SELIC | Busca taxas diárias reais na API do BCB |
| 4b | IBOVESPA | Baixa o índice `^BVSP` via yfinance |
| 5 | MME200 | Calcula a EMA para cada ativo + gráfico de zonas |
| 6 | Motor | Simulação dia a dia da carteira e dos 3 benchmarks |
| 7 | Métricas | CAGR, drawdown, Sharpe, alpha vs benchmarks (terminal) |
| 7b | Dashboard | Painel visual interativo com gráficos e tabela |
| 8 | Gráfico 1 | Evolução patrimonial vs CDI, SELIC, IBOV e aportado |
| 9 | Gráfico 2 | Composição do patrimônio (ações vs caixa/CDB) |
| 10 | Gráfico 3 | Drawdown histórico da carteira |
| 11 | Relatório | Posição final por ativo com cotas, valor e proventos |
| 12 | Export CSV | Salva o histórico diário completo e faz download |

---

## 🖥️ Dashboard interativo (Célula 7b)

Ao executar a Célula 7b, um painel HTML é renderizado diretamente no Colab com:

- **8 KPI cards** — patrimônio, CAGR, drawdown, Sharpe, alpha, volatilidade, proventos, lucro
- **4 cards de benchmark** — montante final em R$ de cada estratégia e quanto a carteira superou
- **5 gráficos** — evolução patrimonial, barras de montante, CAGR horizontal, composição donut, drawdown
- **Tabela por ativo** — cotas, valor, caixa, proventos e % da carteira

---

## 📦 Dependências

| Biblioteca | Versão mínima | Uso |
|-----------|--------------|-----|
| `yfinance` | 0.2+ | Cotações e dividendos da B3 + IBOVESPA |
| `pandas` | 1.5+ | Manipulação de séries temporais |
| `numpy` | 1.23+ | Cálculos matemáticos e estatísticos |
| `matplotlib` | 3.6+ | Gráficos de evolução, composição e drawdown |
| `requests` | 2.28+ | Chamadas à API do Banco Central do Brasil |

Todas são instaladas automaticamente pela Célula 1:

```python
!pip install yfinance pandas numpy matplotlib requests -q
```

---

## 🔌 Fontes de dados

| Dado | Fonte | Método |
|------|-------|--------|
| Preços e dividendos | Yahoo Finance | `yfinance.download()` |
| IBOVESPA | Yahoo Finance (`^BVSP`) | `yfinance.download()` |
| Taxa CDI diária | BCB — Série SGS 12 | API REST pública |
| Taxa SELIC diária | BCB — Série SGS 11 | API REST pública |

> **Sem API key necessária.** Todas as fontes são públicas e gratuitas.

---

## ⚙️ Personalização avançada

### Trocar o período dinamicamente

Por padrão o notebook calcula os últimos 10 anos a partir de hoje. Para fixar um período:

```python
DATA_INICIO = "2018-01-01"
DATA_FIM    = "2024-01-01"
```

### Ajustar a velocidade de compra (DCA)

```python
DEPLOY_DIARIO = 1 / 10   # esvazia o caixa em ~10 pregões (mais agressivo)
DEPLOY_DIARIO = 1 / 40   # esvazia o caixa em ~40 pregões (mais conservador)
```

### Simular CDB acima de 100% do CDI

```python
TAXA_CDB = 1.12   # simula CDB pagando 112% do CDI
```

### Testar com mais ou menos ativos

```python
# 2 ativos
TICKERS = ["WEGE3.SA", "ITUB4.SA"]

# 6 ativos
TICKERS = ["ITSA4.SA", "WEGE3.SA", "BBAS3.SA", "TAEE11.SA", "VALE3.SA", "PETR4.SA"]
```

> O aporte mensal é sempre dividido **igualmente** entre todos os ativos da lista.

---

## ⚠️ Aviso importante

Este projeto tem **finalidade educacional**. Resultados passados não garantem resultados futuros. O backtest não considera:

- Imposto de Renda sobre ganho de capital e dividendos
- Custos de corretagem e emolumentos
- Spread entre compra e venda (bid/ask)
- Liquidez real dos ativos em momentos de crise

**Consulte um profissional certificado antes de tomar decisões de investimento.**

---

## 📄 Licença

Este projeto está licenciado sob a [MIT License](LICENSE) — fique à vontade para usar, modificar e distribuir com os devidos créditos.

---

<div align="center">
  Feito com ❤️ por <strong>Monte Carlo Multimidia</strong>
</div>
