# Relatório — Processamento Paralelo de Logs com Modelo Produtor-Consumidor

**Disciplina:**  PROGRAMACAO CONCORRENTE E DISTRIBUIDA
**Aluno(s):** Amanda Ramos
**Professor:** Rafael
**Data:** 25/03/2026

---

## 1. Descrição do Problema

O programa processa grandes volumes de arquivos de texto contendo logs operacionais. Cada arquivo é analisado para extrair informações relevantes utilizadas em relatórios gerenciais: contagem de linhas, palavras, caracteres e ocorrências das palavras-chave `erro`, `warning` e `info`.

A versão serial percorre os arquivos sequencialmente, um por vez. A versão paralela implementa o **modelo Produtor-Consumidor com buffer limitado** usando `multiprocessing` do Python:


| Questão | Resposta |
|---|---|
| Objetivo do programa | Extrair métricas de logs (linhas, palavras, caracteres, keywords) |
| Volume de dados processado | 1.000 arquivos · 10.000.000 linhas · 200.000.000 palavras · ~1,37 GB |
| Algoritmo utilizado | Produtor-Consumidor com fila limitada (`multiprocessing.Queue`) |
| Complexidade aproximada | O(N · L · W) — N arquivos, L linhas, W palavras por linha |
| Objetivo da paralelização | Processar múltiplos arquivos simultaneamente em núcleos distintos |

---

## 2. Ambiente Experimental

| Item | Descrição |
|---|---|
| Processador | 12th Gen Intel(R) Core(TM) i7-12700   2.10 GHz|
| Número de núcleos | 12|
| Memória RAM | 16,0 GB (utilizável: 15,7 GB)|
| Sistema Operacional | Windows 11 |
| Linguagem utilizada | Python 3.x |
| Biblioteca de paralelização | `multiprocessing` (biblioteca padrão do Python) |
| Versão do Python | Python 3.11 |

---

## 3. Metodologia de Testes

### Como o tempo foi medido

Os testes foram conduzidos medindo o tempo total de execução, desde a abertura do primeiro arquivo até a consolidação final dos resultados.

Configurações: Testes realizados com 1 (serial), 2, 4, 8 e 12 processos.

Métrica: Tempo total de parede (wall time) em segundos.

Condições: Execução em máquina local com carga mínima de processos em segundo plano.

### Configurações testadas

| Configuração | Descrição |
|---|---|
| 1 processo (serial) | Processamento sequencial, sem paralelismo |
| 2 processos | 2 consumidores em paralelo |
| 4 processos | 4 consumidores em paralelo |
| 8 processos | 8 consumidores em paralelo |
| 12 processos | 12 consumidores em paralelo |

### Procedimento experimental

- **Repetições por configuração:** 3 execuções
- **Cálculo do tempo:** média aritmética das 3 repetições
- **Tamanho da entrada:** 1.000 arquivos · 10.000.000 linhas · 1.366.663.305 caracteres (~1,37 GB)
- **Condições:** máquina local, Windows, terminal PowerShell

---

## 4. Resultados Experimentais

Nº Threads/Processos,Tempo de Execução (s)
1 (Serial),22.2512
2,11.4699
4,5.8515
8,3.2899
12,2.7867

---

## 5. Cálculo de Speedup e Eficiência

**Speedup:**

```
Speedup(p) = T(1) / T(p)
```

**Eficiência:**

```
Eficiência(p) = Speedup(p) / p
```

---

## 6. Tabela de Resultados

| Processos | Tempo (s) | Speedup | Eficiência |
|---|---|---|---|
| 1 | 21,8110 | 1,00 | 1,0000 |
| 2 | _(preencher)_ | _(preencher)_ | _(preencher)_ |
| 4 | 5,7725 | **3,78** | **0,9450** |
| 8 | _(preencher)_ | _(preencher)_ | _(preencher)_ |
| 12 | _(preencher)_ | _(preencher)_ | _(preencher)_ |

> **Speedup com 4 workers:** `21,8110 / 5,7725 = 3,78x`
> **Eficiência com 4 workers:** `3,78 / 4 = 0,945 (94,5%)`

---

## 7. Resultado Consolidado Verificado

Ambas as versões (serial e paralela) produziram **resultado idêntico**, confirmando a corretude da implementação paralela:

```
=== RESULTADO CONSOLIDADO ===
Total de linhas:      10.000.000
Total de palavras:   200.000.000
Total de caracteres: 1.366.663.305

Contagem de palavras-chave:
  erro:    33.332.083
  warning: 33.330.520
  info:    33.329.065
```

---

## 8. Gráfico de Tempo de Execução

- **Eixo X:** número de processos
- **Eixo Y:** tempo de execução (segundos)

```
Tempo(s)
  25 |█ 21,81
  20 |█
  15 |█
  10 |█
   5 |█                 █ 5,77
   0 +------------------+--------> Processos
          1             4
```

_(Inserir gráfico gerado pelo benchmark para 2, 8 e 12 workers após coletar os demais tempos)_

---

## 9. Gráfico de Speedup

- **Eixo X:** número de processos
- **Eixo Y:** speedup
- **Linha tracejada:** speedup ideal (linear)

```
Speedup
  12 |                              / ← ideal
   8 |                         /
   4 |              *  3,78x  /
   2 |         /
   1 |*  1,0x /
   0 +--------------------------> Processos
       1    2    4    8    12
```

> Com 4 workers, o speedup de **3,78x** ficou muito próximo do ideal (4,0x), indicando alta eficiência de 94,5%.

---

## 10. Gráfico de Eficiência

- **Eixo X:** número de processos
- **Eixo Y:** eficiência (0 a 1)

```
Eficiência
  1,00 |*
  0,94 |          * 0,945
  0,80 |
  0,60 |
       +--------------------------> Processos
         1         4
```

---

## 11. Análise dos Resultados

### O speedup obtido foi próximo do ideal?

**Sim, muito próximo.** Com 4 workers, o speedup de **3,78x** representa **94,5% do ideal teórico (4,0x)**. Isso demonstra que, com volume de dados suficiente (~1,37 GB, 1.000 arquivos), o overhead do modelo Produtor-Consumidor torna-se insignificante frente ao ganho de processamento paralelo.

### A aplicação apresentou escalabilidade?

Sim. A redução de **21,81s → 5,77s** (queda de 73,5% no tempo total) com apenas 4 processos confirma escalabilidade efetiva para cargas de trabalho reais.

### Em qual ponto a eficiência começou a cair?

Com os dados disponíveis, a eficiência com 4 workers é de **94,5%** — excelente. Espera-se que comece a cair a partir do ponto em que `workers > núcleos físicos`, devido ao overhead de escalonamento do sistema operacional.

### Overhead de paralelização

| Fonte de overhead | Impacto observado |
|---|---|
| Criação de processos | Desprezível frente a 21s de processamento |
| Serialização via `Queue` | Baixo — resultados por arquivo são pequenos dicionários |
| Buffer limitado (`maxsize=4`) | Controlado — backpressure evita saturação de memória |
| Sentinel pattern | Negligenciável |

### O número de processos ultrapassa os núcleos físicos?

Nas configurações de 8 e 12 processos isso depende do hardware. Em máquinas com 4–6 núcleos, ultrapassar esse limite tende a degradar o speedup por contenção de CPU e troca de contexto excessiva.

---

## 12. Conclusão

O experimento demonstrou que o modelo **Produtor-Consumidor com buffer limitado** é altamente eficaz para o processamento paralelo de logs em larga escala:

- Com **4 workers** e ~1,37 GB de dados, o speedup foi de **3,78x** com eficiência de **94,5%**;
- O tempo caiu de **21,81s → 5,77s** — redução de **73,5%**;
- A corretude foi confirmada: serial e paralelo produziram resultados **idênticos**.

**Melhor número de processos:** igual ao número de núcleos físicos da máquina.

**O programa escala bem?** Sim — a arquitetura é naturalmente escalável. O gargalo é hardware, não a implementação.

**Melhorias possíveis:**

- Aumentar `BUFFER_SIZE` para máquinas com muita RAM e arquivos muito grandes;
- Usar `concurrent.futures.ProcessPoolExecutor` para gestão automática de workers;
- Implementar leitura assíncrona de disco (`asyncio`) para reduzir tempo de I/O;
- Dividir arquivos muito grandes em chunks para paralelismo intra-arquivo.

---

## Como Executar

```bash
# Versão serial
python serial_processor.py log1/

# Versão paralela
python parallel_processor.py log1/ --workers 2
python parallel_processor.py log1/ --workers 4
python parallel_processor.py log1/ --workers 8
python parallel_processor.py log1/ --workers 12

# Benchmark completo (serial + 2/4/8/12 workers, 3 repetições cada)
python benchmark_completo.py log1/
```

## Estrutura do Projeto

```
log/
├── serial_processor.py      # Processamento sequencial
├── parallel_processor.py    # Modelo Produtor-Consumidor
├── benchmark_completo.py    # Benchmark + análise de speedup
├── README.md                # Este relatório
└── log1/
    ├── arquivo_0.txt
    └── arquivo_1.txt
```
