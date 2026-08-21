# μFé-8 EX Core (Extended Architecture)

Uma implementação e extensão independente da arquitetura do processador de 8 bits **μFé-8**. O projeto serve como um elo entre conceitos de **Sistemas Digitais** e **Sistemas Microprocessados**.

> **Créditos e Referência**: Este projeto é baseado na especificação e arquitetura original criada por [@dccafe no projeto uFe8](https://github.com/dccafe/uFe8).

---

## 📌 Visão Geral da Arquitetura

O **μFé-8** é um microcontrolador de 8 bits inspirado na arquitetura simplificada do MSP430.

* **Barramento de Dados e Endereços**: 8 bits
* **Registradores de Uso Geral**: `R0`, `R1`, `R2`, `R3`
* **Registradores Especiais Internos**:
  * `PC` (Program Counter)
  * `IR` (Instruction Register)
  * `CTE` (Constant Register)
  * **`SP` (Stack Pointer)** *(Extensão desta versão)*

### 🗺️ Mapa de Memória

| Faixa de Endereços | Região | Descrição |
|---|---|---|
| `0x00` - `0x7F` | **ROM** | Instruções do programa e constantes |
| `0x80` - `0xBF` | **RAM** | Variáveis de dados e Pilha (Stack) |
| `0xC0` - `0xFF` | **Periféricos** | E/S e módulos de hardware (GPIO, etc.) |

---

## 🚀 Extensão Proposta: Suporte a Sub-rotinas (Pilha, `CALL` e `RET`)

Nesta versão customizada, a arquitetura original foi estendida para suportar **chamadas de funções/sub-rotinas** nativas através de uma pilha (*Stack*) na memória RAM.

### 1. Ponteiro de Pilha (`SP`)
* Registrador de 8 bits inicializado no topo da memória RAM (`0xBF`).
* Cresce no sentido inverso da memória (decrementa na escrita/empilhamento e incrementa na leitura/desempilhamento).

### 2. Novas Instruções

| Mnemônico | Opcode (Sugerido) | Operação | Descrição |
|---|---|---|---|
| `CALL label` | `111 1 xx 100` | `RAM[SP] <= PC + 2`<br>`SP <= SP - 1`<br>`PC <= label` | Salva o endereço de retorno na pilha e salta para a sub-rotina. |
| `RET` | `111 1 xx 101` | `SP <= SP + 1`<br>`PC <= RAM[SP]` | Desempilha o endereço de retorno e retorna da sub-rotina. |

---

## 🏗️ Conjunto de Instruções da ISA Base

As instruções ocupam 1 byte, podendo ter 1 byte adicional em caso de uso de constante imediata (`#i`).

| Opcode | C | SRC | DST | Mnemônico | Detalhes |
|---|---|---|---|---|---|
| `000` | c | ss | dd | `ADD Rs/#i, Rd` | `[Rs\|#i] + Rd => Rd` |
| `001` | c | ss | dd | `AND Rs/#i, Rd` | `[Rs\|#i] & Rd => Rd` |
| `010` | c | ss | dd | `XOR Rs/#i, Rd` | `[Rs\|#i] ^ Rd => Rd` |
| `011` | c | ss | dd | `OR  Rs/#i, Rd` | `[Rs\|#i] \| Rd => Rd` |
| `100` | c | ss | dd | `LD  @Rs/@i, Rd` | `@Rs/@i => Rd` |
| `101` | c | ss | dd | `ST  Rs/#i, @Rd` | `Rs/#i => @Rd` |
| `110` | c | ss | dd | `MOV Rs/#i, Rd` | `[Rs\|#i] => Rd` |
| `111` | 1 | xx | 00 | `JC label` | `PC <= label` se `C == 1` |
| `111` | 1 | xx | 01 | `JZ label` | `PC <= label` se `Z == 1` |
| `111` | 1 | xx | 10 | `JNZ label` | `PC <= label` se `Z == 0` |
| `111` | 1 | xx | 11 | `JMP label` | `PC <= label` |

---

## 🛠️ Organização do Repositório

```text
.
├── docs/               # Especificações técnicas e esquemáticos da FSM
├── logisim/            # Circuitos e simulações (.circ)
├── rtl/                # Códigos em Verilog/SystemVerilog (opcional)
├── tools/              # Assembler em Python para gerar arquivos HEX para a ROM
├── .gitignore
├── LICENSE
└── README.md
