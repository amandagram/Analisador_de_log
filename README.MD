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

|Nº Threads|Processos,Tempo de Execução (s)|
|---|---|
| 1 processo (serial) | 22.2512 |
| 2 processos | 11.4699 |
| 4 processos |  8515 |
| 8 processos | 2899 |
| 12 processos |  7867 |
---

## 5. Cálculo de Speedup e Eficiência

Utilizando as fórmulas de Speedup ($S = T_1 / T_p$) e Eficiência ($E = S / p$):
|Threads/ProcessosTempo |(s)Speedup |Eficiência|
|---|---|
|1|22.25|1.00|1.00|
|2|11.47|1.94|0.97|
|4|5.85|3.80|0.95|
|8|3.29|6.76|0.84|
|12|2.79|7.98|0.66|
---

## 6. Tabela de Resultados
Threads/Processos	Tempo (s)	Speedup	Eficiência
|---|---|
|1|22.25|1.00|1.00|
|2|11.46|1.94|0.97|
|4|5.85|3.80|0.95|
|8|3.29|6.76|0.84|
|12|2.79|7.98|0.66|

## 7. Resultado Consolidado Verificado

Tempo (s)
 ^
22.25 | * (1 Proc)
      |
11.47 |       * (2 Proc)
      |
 5.85 |             * (4 Proc)
      |
 3.29 |                   * (8 Proc)
 2.79 |                         * (12 Proc)
      +---------------------------------------> Processos
        1     2     4     8     12
---

## 8. Gráfico de Tempo de Execução

- **Eixo X:** número de processos
- **Eixo Y:** tempo de execução (segundos)

Tempo (s)
 ^
22.25 | * (1 Proc)
      |
11.47 |       * (2 Proc)
      |
 5.85 |             * (4 Proc)
      |
 3.29 |                   * (8 Proc)
 2.79 |                         * (12 Proc)
      +---------------------------------------> Processos
        1     2     4     8     12
---

## 9. Gráfico de Speedup

- **Eixo X:** número de processos
- **Eixo Y:** speedup
- **Linha tracejada:** speedup ideal (linear)

```
Speedup
 ^                                          (I) Ideal
12 |                                       /
   |                                     /
10 |                                   /
   |                                 /
 8 |                               /   * (A) Alcançado: 7.98
   |                             /  *
 6 |                           / *
   |                         / *
 4 |                 * (A) /
   |               /
 2 |       * (A) /
   |     /
 1 | * /
   +---------------------------------------> Processos
     1     2     4     8     12
```



---

## 10. Gráfico de Eficiência

- **Eixo X:** número de processos
- **Eixo Y:** eficiência (0 a 1)

```
Eficiência
 ^
1.0 | * (1)   * (2: 0.97)
    |               * (4: 0.95)
0.8 |                     * (8: 0.84)
    |
0.6 |                           * (12: 0.66)
    |
0.4 |
    +---------------------------------------> Processos
      1       2       4       8       12
```

---

## 11. Análise dos Resultados


Desempenho Próximo ao Ideal: Até 4 processos, o speedup de 3.80x é quase linear (95% de eficiência), indicando que o algoritmo é altamente paralelizável.

Escalabilidade e Gargalo: A eficiência cai para 66% ao usar 12 processos. Isso ocorre porque o ganho de CPU começa a ser superado pelo custo de coordenação (overhead) e, principalmente, pela disputa de leitura no disco (I/O Bound).

Hardware: Como a eficiência cai drasticamente após 8 processos, é provável que a CPU física possua 8 núcleos reais, e o uso de 12 processos esteja competindo por recursos de Hyper-Threading ou excedendo a largura de banda do sistema de arquivos.
---

## 12. Conclusão

O experimento demonstrou que o modelo **Produtor-Consumidor com buffer limitado** é altamente eficaz para o processamento paralelo de logs em larga escala:

O projeto demonstra que a paralelização é essencial para o processamento de grandes volumes de logs. O tempo total foi reduzido em 87.4% (de 22.2s para 2.7s). Para este ambiente específico, a configuração de 8 processos mostrou-se a mais equilibrada entre ganho de performance e uso de recursos.

**Melhor número de processos:** igual ao número de núcleos físicos da máquina.

**O programa escala bem?** Sim — a arquitetura é naturalmente escalável. O gargalo é hardware, não a implementação.

---


