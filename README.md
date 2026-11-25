# SISTEMA-DE-GERENCIAMENTO-DE-SENHAS-DE-ATENDIMENTO-CL-NICO
# Sistema de Gerenciamento de Senhas de Atendimento Clínico

Projeto da **Questão 02** da avaliação, desenvolvido em linguagem C.

## Objetivo

Informatizar a triagem e a organização das filas de uma clínica: cadastrar pacientes, classificá-los automaticamente por prioridade, emitir senhas sequenciais e controlar a ordem de chamada e atendimento.


## Funcionalidades

- Cadastro do paciente (gênero, idade, gestante, lactante, necessidade especial, obesidade) com validação de entrada.
- Classificação automática em **Urgente**, **Preferencial** ou **Normal**.
- Numeração independente por categoria (`U001, U002… / P001… / N001…`).
- Consulta da próxima senha, chamada, identificação da senha em atendimento e finalização.
- Painel textual com senha atual, categoria, próximas senhas e quantidade em espera por categoria.
- Cadastro de vários pacientes durante a execução (até 200 por fila).

## Regras de classificação

| Código | Categoria    | Condição |
|--------|--------------|----------|
| U001   | Urgente      | Idade **superior a 80 anos** |
| P001   | Preferencial | Gestantes, lactantes, necessidades especiais, obesos, mulheres ≥ 60 anos, homens ≥ 65 anos |
| N001   | Normal       | Demais pacientes |

**Ordem de chamada:** Urgente → Preferencial → Normal. Dentro de cada categoria vale a ordem de emissão (**FIFO**). Senhas já atendidas não voltam à fila.

As perguntas sobre gestante e lactante só são feitas quando o gênero informado é feminino.

## Compilação e execução

Requer `gcc`.

```bash
gcc -Wall -Wextra -std=c99 -o senhas main.c
./senhas            # Linux / macOS
senhas.exe          # Windows
```

## Estruturas de dados

- `Paciente`: dados informados no cadastro.
- `Senha`: código, categoria, número e paciente.
- `Fila`: fila circular (vetor) com `inicio` e `tamanho`; uma por categoria.
- `Sistema`: três filas, contadores independentes, senha atual e total de atendidos.

## Funções implementadas

| Função | Responsabilidade |
|--------|------------------|
| `lerLinha`, `lerInteiro`, `lerSimNao`, `lerGenero` | Leitura e validação de entradas |
| `cadastrarPaciente` | Coleta dos dados do paciente |
| `classificarPaciente` | Define a categoria conforme as regras |
| `gerarSenha` / `registrarSenha` | Cria a senha sequencial e a insere na fila |
| `enfileirar` / `desenfileirar` | Operações da fila circular |
| `proximaCategoria` / `totalEmEspera` | Controle da ordem de prioridade |
| `consultarProxima` | Mostra a próxima senha sem chamá-la |
| `chamarPaciente` | Retira a próxima senha da fila e inicia o atendimento |
| `identificarEmAtendimento` | Mostra a senha em atendimento |
| `finalizarAtendimento` | Conclui o atendimento e atualiza a fila |
| `exibirPainel` | Painel textual de chamada |
| `exibirMenu` / `main` | Menu interativo e coordenação do fluxo |

## Exemplo de utilização

Cadastro de: homem 30 anos (normal), gestante 25 anos, homem 85 anos, homem 70 anos, mulher 20 anos.

```
>>> Senha emitida: N001 (Normal)
>>> Senha emitida: P001 (Preferencial)
>>> Senha emitida: U001 (Urgente)
>>> Senha emitida: P002 (Preferencial)
>>> Senha emitida: N002 (Normal)

Proximas senhas: U001 P001 P002 N001 N002
Em espera: Urgente 1 | Preferencial 2 | Normal 2
```

Chamadas sucessivas ocorrem na ordem: `U001 → P001 → P002 → N001 → N002`.

```
*** Chamando senha U001 (Urgente) ***
Ja existe a senha U001 em atendimento. Finalize-o antes de chamar outra.
Atendimento da senha U001 finalizado.
*** Chamando senha P001 (Preferencial) ***
```

## Validações e testes realizados

- Opções de menu, idade (0–130), gênero (F/M) e respostas S/N inválidas são rejeitadas com nova solicitação.
- Chamar sem fila, finalizar sem atendimento e chamar com atendimento em curso exibem mensagens de erro.
- Testada a ordem de prioridade com senhas emitidas fora de ordem de categoria e a independência da numeração.
