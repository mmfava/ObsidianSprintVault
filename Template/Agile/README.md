---
created: 2025-03-04T16:04
updated: 2025-03-04T17:09
---

# Templates Agile

Este diretório contém os templates para automatizar a criação das notas e da estrutura de uma sprint no Obsidian, usando o plugin [Templater](https://github.com/SilentVoid13/Templater). O foco principal é o template **Sprint-Start.md**, que inicia a configuração da sprint com uma sequência intuitiva de perguntas.

[![Assista à demonstração do Sprint-Start](https://img.youtube.com/vi/UmGCHRNbGAA/0.jpg)](https://www.youtube.com/watch?v=UmGCHRNbGAA)

## Pré-requisitos

1. **Adicionar o comando "Templater: Create new note from template" à ribbon do vault**  
    Permite criar notas a partir de um template pré-definido de forma rápida e prática.

[![Demonstração - Comando na Ribbon](https://img.youtube.com/vi/E55qtFk39Oo/0.jpg)](https://www.youtube.com/watch?v=E55qtFk39Oo)

2. **Configurar o plugin "Tasks" para identificar tarefas na sprint**  
    O plugin será ajustado para que itens de checklist com a hash `#task` sejam reconhecidos como tarefas da sprint. Por exemplo, itens formatados como "- [ ] #task Isso é uma tarefa" serão tratados como tarefas, enquanto "- [ ] É um item em uma lista, mas não uma tarefa da sprint" não serão.

[![Demonstração - Configuração do Plugin Tasks](https://img.youtube.com/vi/kwKm2qgWguU/0.jpg)](https://www.youtube.com/watch?v=kwKm2qgWguU)


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

