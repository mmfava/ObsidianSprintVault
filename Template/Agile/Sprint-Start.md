
<%*
/* =====================================================
   Parte 1: Seleção do Projeto e Criação da Subpasta da Sprint
   ===================================================== */

// Obtém todas as pastas do vault e filtra as que estão na raiz
const allFolders = app.vault.getAllLoadedFiles().filter(f => f instanceof tp.obsidian.TFolder);
const rootFolders = allFolders.filter(folder => folder.path.split("/").length === 1);

// Exibe uma lista das pastas da raiz para o usuário selecionar o projeto
let selectedFolder = await tp.system.suggester(item => item.path, rootFolders);

// Se nenhuma pasta for selecionada, solicita o nome do projeto e cria a pasta, se necessário
if (!selectedFolder) {
  const folderName = await tp.system.prompt("Digite o nome do Projeto:");
  const folderPath = folderName.trim();
  if (!app.vault.getAbstractFileByPathInsensitive(folderPath)) {
    await app.vault.createFolder(folderPath);
  }
  selectedFolder = { path: folderPath };
}

// Solicita ao usuário o nome ou número da sprint atual
const sprint = await tp.system.prompt("Digite o nome ou número da Sprint atual:");
const sprintFolderPath = `${selectedFolder.path}/Sprint-${sprint.trim()}`;

// Se a subpasta da sprint já existir, exibe um alerta e aborta o script
if (app.vault.getAbstractFileByPathInsensitive(sprintFolderPath)) {
  new Notice("Diretório da sprint já existe. A operação foi abortada.");
  return;
}

// Caso contrário, cria a subpasta da sprint
await app.vault.createFolder(sprintFolderPath);

// Move o arquivo atual para a subpasta da sprint, se necessário:
await tp.file.move(`${sprintFolderPath}/${tp.file.title}`);

await tp.file.rename("Start")

/* =====================================================
   Parte 2: Captura de Metadados Adicionais para a Sprint
   ===================================================== */

// Solicita ao usuário a data de início da sprint (formato: YYYY-MM-DD)
const startDateInput = await tp.system.prompt("Digite a data de início da Sprint (YYYY-MM-DD):");
const startDate = moment(startDateInput, "YYYY-MM-DD");

// Solicita a duração da sprint (em semanas)
const durationWeeksInput = await tp.system.prompt("Digite a duração da Sprint (em semanas):");
const durationWeeks = parseInt(durationWeeksInput);

// Solicita a frequência das dailies (dias por semana)
const dailyFrequencyInput = await tp.system.prompt("Digite a frequência das dailies (dias por semana):");
const dailyFrequency = parseInt(dailyFrequencyInput);

// Calcula a data final da sprint: (duração em semanas * 7) - 1 dia
const endDate = startDate.clone().add(durationWeeks * 7 - 1, "days");

// Calcula o total de dailies na sprint
const totalDailies = dailyFrequency * durationWeeks;

/* =====================================================
   Parte 3: Criação do Arquivo "planning.md" com Template "Sprint-Planning"
   ===================================================== */

// Define o nome fixo do arquivo e o caminho completo (incluindo extensão)
const newFileName = "Planning";
const filePath = `${sprintFolderPath}/${newFileName.trim()}`;

// Verifica se o arquivo já existe; se não, cria-o utilizando o template "Sprint-Planning"
let newFile = tp.file.find_tfile(filePath);
if (!newFile) {
  const templateFile = tp.file.find_tfile("Sprint-Planning");
  if (!templateFile) {
    new Notice("Template 'Sprint-Planning' não encontrado.");
    return;
  }
  newFile = await tp.file.create_new(templateFile, filePath);
}

// Atualiza o frontmatter do novo arquivo com os metadados capturados
await app.fileManager.processFrontMatter(newFile, (frontmatter) => {
  frontmatter.projeto = selectedFolder.path;
  frontmatter.sprint = sprint.trim();
  frontmatter.start = startDate.format("YYYY-MM-DD");
  frontmatter.end = endDate.format("YYYY-MM-DD");
  frontmatter.dailies = totalDailies;
  frontmatter.tags = []; // Ajuste conforme necessário
});

// Armazena o basename do novo arquivo para exibir um link
const createdFileDisplay = newFile.basename;
_%>


<%*
/* ===================================================== */
/*   Parte 4: Criação das notas de Daily para a Sprint   */
/* ===================================================== */

// Define o caminho para a subpasta "Daily" dentro da pasta da sprint.
const dailyFolderPath = `${sprintFolderPath}/Daily`;

// Se a pasta "Daily" não existir, cria-a.
if (!app.vault.getAbstractFileByPathInsensitive(dailyFolderPath)) {
  await app.vault.createFolder(dailyFolderPath);
}

// Loop para criar um arquivo para cada daily (de 1 até totalDailies)
for (let i = 1; i <= totalDailies; i++) {
  // Define o nome do arquivo: "Daily-<número>"
  const dailyFileName = `Daily-${i}`;
  
  // Monta o caminho completo para o novo arquivo (a extensão ".md" é criada por default)
  const dailyFilePath = `${dailyFolderPath}/${dailyFileName}`;
  
  // Verifica se o arquivo já existe
  let dailyFile = tp.file.find_tfile(dailyFilePath);
  if (!dailyFile) {
    // Busca o template "Sprint-Daily"
    const templateDaily = tp.file.find_tfile("Sprint-Daily");
    if (!templateDaily) {
      new Notice("Template 'Sprint-Daily' não encontrado.");
      return;
    }
    // Cria o novo arquivo utilizando o template "Sprint-Daily"
    dailyFile = await tp.file.create_new(templateDaily, dailyFilePath);
  }
  
  // Calcula a data para este daily: startDate + (i - 1) dias
  // (Caso a lógica de frequência seja diferente, ajuste o cálculo)
  // const dailyDate = startDate.clone().add(i - 1, 'days').format("YYYY-MM-DD");
  
  // Atualiza o frontmatter do arquivo da daily com os metadados necessários
  await app.fileManager.processFrontMatter(dailyFile, (frontmatter) => {
    frontmatter.projeto = selectedFolder.path;
    frontmatter.sprint = sprint.trim();
    frontmatter.daily = i;
    // frontmatter.date = dailyDate;
    frontmatter.type = "daily";
    frontmatter.tags = []; // Ajuste ou adicione tags conforme necessário
  });
}
_%>

<%*
/* =====================================================
   Parte 5: Criação dos Arquivos de Review e Retro
   ===================================================== */

// --- Criação do arquivo de Sprint Review ---

// Define o caminho para o arquivo de review (não adicionamos ".md", pois é criado como markdown por default)
const reviewFilePath = `${sprintFolderPath}/Sprint-Review`;
let reviewFile = tp.file.find_tfile(reviewFilePath);
if (!reviewFile) {
  // Procura o template "Sprint-Review"
  const templateReview = tp.file.find_tfile("Sprint-Review");
  if (!templateReview) {
    new Notice("Template 'Sprint-Review' não encontrado.");
    return;
  }
  // Cria o arquivo usando o template "Sprint-Review"
  reviewFile = await tp.file.create_new(templateReview, reviewFilePath);
}

// Atualiza o frontmatter do arquivo de review
await app.fileManager.processFrontMatter(reviewFile, (frontmatter) => {
  frontmatter.projeto = selectedFolder.path;
  frontmatter.sprint = sprint.trim();
  frontmatter.date = "";  // Deixe vazio para preenchimento manual
  frontmatter.type = "review";
  frontmatter.tags = [];  // Ajuste conforme necessário
});

// --- Criação do arquivo de Sprint Retro (Retrospectiva) ---

const retroFilePath = `${sprintFolderPath}/Sprint-Retro`;
let retroFile = tp.file.find_tfile(retroFilePath);
if (!retroFile) {
  // Procura o template "Sprint-Retro"
  const templateRetro = tp.file.find_tfile("Sprint-Retro");
  if (!templateRetro) {
    new Notice("Template 'Sprint-Retro' não encontrado.");
    return;
  }
  // Cria o arquivo usando o template "Sprint-Retro"
  retroFile = await tp.file.create_new(templateRetro, retroFilePath);
}

// Atualiza o frontmatter do arquivo de retrospectiva
await app.fileManager.processFrontMatter(retroFile, (frontmatter) => {
  frontmatter.projeto = selectedFolder.path;
  frontmatter.sprint = sprint.trim();
  frontmatter.date = "";  // Deixe vazio para preenchimento manual
  frontmatter.type = "retrospectiva";
  frontmatter.tags = [];
});
_%>

<%*
/* =====================================================
   Parte 6: Criação dos Diretórios "Meetings" e "Tasks"
   ===================================================== */

// Define o caminho para o diretório "Meetings" dentro da pasta da sprint
const meetingsFolderPath = `${sprintFolderPath}/Meetings`;
// Se a pasta "Meetings" não existir, cria-a
if (!app.vault.getAbstractFileByPathInsensitive(meetingsFolderPath)) {
  await app.vault.createFolder(meetingsFolderPath);
  new Notice(`Diretório "Meetings" criado em ${meetingsFolderPath}`);
}

// Define o caminho para o diretório "Tasks" dentro da pasta da sprint
const tasksFolderPath = `${sprintFolderPath}/Tasks`;
// Se a pasta "Tasks" não existir, cria-a
if (!app.vault.getAbstractFileByPathInsensitive(tasksFolderPath)) {
  await app.vault.createFolder(tasksFolderPath);
  new Notice(`Diretório "Tasks" criado em ${tasksFolderPath}`);
}
_%>

<%*
// Listar as tasks
// Define o caminho para o arquivo "Sprint-Tasks.md"
const sprintTasksFilePath = `${tasksFolderPath}/Sprint-Tasks`;

let taskFile = tp.file.find_tfile(sprintTasksFilePath);
if (!taskFile) {
  // Procura o template "Sprint-Retro"
  const templateTask = tp.file.find_tfile("Sprint-Tasks");
  if (!templateTask) {
    new Notice("Template 'Sprint-Tasks' não encontrado.");
    return;
  }
  // Cria o arquivo usando o template "Sprint-Retro"
  taskFile = await tp.file.create_new(templateTask, sprintTasksFilePath);
}

// Atualiza o frontmatter do arquivo de retrospectiva
await app.fileManager.processFrontMatter(taskFile, (frontmatter) => {
  frontmatter.projeto = selectedFolder.path;
  frontmatter.sprint = sprint.trim();
});

// Substitui pelo path da Sprint para extrair as tasks 
await app.vault.process(taskFile, (data) => {
  return data.replace("$PATH-TO-SPRINT-PROJECT", `${sprintFolderPath}`);
});

// Substitui pelo path da Sprint para extrair as tasks 
await app.vault.process(taskFile, (data) => {
  return data.replace("$PATH-TO-SPRINT-PROJECT", `${sprintFolderPath}`);
});

_%>




1. [[<% createdFileDisplay %>]]
2. Daily notes criadas em: [[<% dailyFolderPath %>]]
3. [Sprint Review](<% reviewFilePath %>) 
4. [Sprint Retro](<% retroFilePath %>)
5. [Meetings folder](<% meetingsFolderPath %>)
6. [Tasks folder](<% tasksFolderPath %>)
