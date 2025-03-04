---
created: 2025-03-04T16:04
updated: 2025-03-04T16:45
---

# Templates Agile

Este diretório contém os templates para automatizar a criação das notas e da estrutura de uma sprint no Obsidian, usando o plugin [Templater](https://github.com/SilentVoid13/Templater). O foco principal é o template **Sprint-Start.md**, que inicia a configuração da sprint com uma sequência intuitiva de perguntas.

![](https://youtu.be/UmGCHRNbGAA)

## Pré-requisitos

1) Adicionar o comando "Templater: Create new note from template" à ribbon do vault, permitindo criar notas a partir de um template pré-definido de forma rápida e prática:

![](https://youtu.be/E55qtFk39Oo)


2) Configura o plugin "Tasks" para identificar, por meio da hash "#task", quais itens de checklist devem ser tratados como tarefas da sprint. Por exemplo, itens formatados como "- [ ] #task Isso é uma tarefa" serão reconhecidos como tarefas, enquanto "- [ ] É um item em uma lista, mas não uma tarefa da sprint" não serão.

![](https://youtu.be/kwKm2qgWguU)


## Etapas

Ao executar o **Sprint-Start.md**, você será guiado pelas seguintes etapas:

1. **Seleção do Projeto**
    - É apresentada uma lista das pastas na raiz do vault.
    - Caso o projeto desejado não exista, você pode digitar o nome para criá-lo automaticamente.

2. **Criação da Subpasta da Sprint**
    - Informe o nome ou número da sprint.
    - O script cria uma subpasta com o padrão `Sprint-<nome ou número>` dentro do projeto selecionado.
    - Se a pasta já existir, o processo é abortado para evitar sobrescrita.

3. **Configuração dos Metadados da Sprint**
    - Digite a data de início (formato YYYY-MM-DD).
    - Informe a duração da sprint (em semanas).
    - Defina a frequência das dailies (dias por semana).  
        Com essas informações, o script calcula a data final da sprint e o total de dailies.

4. **Geração dos Arquivos e Diretórios**
    - **Planning:** Cria a nota de planejamento usando o template **Sprint-Planning.md** e atualiza os metadados.
    - **Dailies:** Cria uma subpasta "Daily" com arquivos individuais para cada dia, baseando-se na frequência e duração informadas.
    - **Review & Retro:** Gera arquivos para a Sprint Review (**Sprint-Review.md**) e a Sprint Retro (**Sprint-Retro.md**).
    - **Meetings & Tasks:** Cria os diretórios para notas de reuniões e tasks, e gera o arquivo **Sprint-Tasks.md** para compilar as tasks.

## Organização dos Arquivos

```
Template/
└── Agile/
    ├── Sprint-Daily.md         // Template para dailies
    ├── Sprint-Meeting.md       // Template para reuniões com stakeholders
    ├── Sprint-Planning.md      // Template para planejamento da sprint
    ├── Sprint-Review.md        // Template para review da sprint
    ├── Sprint-Retro.md         // Template para retrospectiva da sprint
    ├── Sprint-Start.md         // Script principal para iniciar a sprint
    └── Sprint-Tasks.md         // Template para compilar as tasks da sprint
```

