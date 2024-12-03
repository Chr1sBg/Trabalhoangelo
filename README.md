
# Trabalho sobre os Padrões de Projeto Command e Strategy 🚀

## 1. Introdução 🌟

### O que são Design Patterns?
- **Soluções reutilizáveis** para problemas comuns no desenvolvimento de software.
- Promovem boas práticas como **reutilização**, **manutenibilidade** e **flexibilidade**.
- Classificados em 3 categorias:
  - 🛠️ **Criacionais**: Como os objetos são criados.
  - 🏗️ **Estruturais**: Organização e composição de classes/objetos.
  - 🔄 **Comportamentais**: Comunicação e interação entre objetos.

### Foco do Trabalho
- Vamos explorar os padrões comportamentais **Command** e **Strategy**.
- Aplicações práticas e comparações. 👨‍💻

---

## 2. Padrão Command 🔧

O padrão Command é um padrão comportamental que permite encapsular uma solicitação como um objeto, permitindo que você parametrize clientes com diferentes requisições, enfileire ou registre solicitações e forneça suporte a operações de desfazer (undo) ou refazer (redo). Em vez de invocar diretamente uma ação em um objeto, o comando é passado como um objeto, o que aumenta a flexibilidade no tratamento das ações.

Principais características:

  - Desacoplamento entre o objeto que invoca a ação e o objeto que executa a ação.
  - Suporte para desfazer e refazer operações.
  - Facilita a adição de novas operações sem modificar o código existente.

### Sem o Padrão Command

```typescript
class TaskExecutor {
    executeTask(task: string): void {
        if (task === "SendEmail") {
            console.log("Email enviado.");
        } else if (task === "GenerateReport") {
            console.log("Relatório gerado.");
        } else {
            console.log("Tarefa desconhecida.");
        }
    }
}

const executor = new TaskExecutor();
executor.executeTask("SendEmail"); 
executor.executeTask("GenerateReport"); 
executor.executeTask("UnknownTask"); 
```

💡 **Problemas**:
- Acoplamento direto entre a lógica de execução e as tarefas específicas.
- Difícil adicionar novas tarefas sem modificar o código.
- Não há suporte para desfazer operações.

### Com o Padrão Command

#### 1. Interface Command
Define o contrato para todas as tarefas.

```typescript
interface Command {
    execute(): void;
    undo(): void;
}
```

#### 2. Comandos Concretos
Cada tarefa é implementada como uma classe concreta que segue a interface `Command`.

```typescript
class SendEmailCommand implements Command {
    execute(): void {
        console.log("Email enviado.");
    }
    undo(): void {
        console.log("Envio de email desfeito.");
    }
}

class GenerateReportCommand implements Command {
    execute(): void {
        console.log("Relatório gerado.");
    }
    undo(): void {
        console.log("Geração de relatório desfeita.");
    }
}
```

#### 3. Invoker (Executor Central)
Gerencia a execução e o histórico de comandos.

```typescript
class TaskManager {
    private history: Command[] = [];

    executeCommand(command: Command): void {
        command.execute();
        this.history.push(command);
    }

    undoLastCommand(): void {
        const lastCommand = this.history.pop();
        if (lastCommand) {
            lastCommand.undo();
        } else {
            console.log("Nada a desfazer.");
        }
    }
}
```

#### 4. Exemplo de Uso

```typescript
const taskManager = new TaskManager();

const sendEmail = new SendEmailCommand();
const generateReport = new GenerateReportCommand();

taskManager.executeCommand(sendEmail); 
taskManager.executeCommand(generateReport); 
taskManager.undoLastCommand(); 
taskManager.undoLastCommand(); 
```

💡 **Benefícios**:
- Fácil adicionar novas tarefas sem modificar o executor.
- Suporte para desfazer operações com o histórico.

---

## 3. Padrão Strategy 🧠

O padrão Strategy é um padrão comportamental que define uma família de algoritmos, encapsula cada um deles e torna-os intercambiáveis. O padrão permite que o algoritmo varie independentemente dos clientes que o utilizam, promovendo flexibilidade e separação de responsabilidades.

Principais características:

  - Permite que o comportamento de um objeto seja alterado em tempo de execução.
  - Facilita a adição de novos algoritmos sem alterar o código existente.
  - Cada algoritmo é encapsulado em uma classe separada, mantendo o código limpo e modular.

### Sem o Padrão Strategy

```typescript
class TaskPrioritizer {
    prioritizeTasks(tasks: string[], strategy: string): void {
        if (strategy === "Alphabetical") {
            tasks.sort();
            console.log("Tarefas ordenadas alfabeticamente:", tasks);
        } else if (strategy === "ByLength") {
            tasks.sort((a, b) => a.length - b.length);
            console.log("Tarefas ordenadas por tamanho:", tasks);
        } else {
            console.log("Estratégia desconhecida.");
        }
    }
}

const prioritizer = new TaskPrioritizer();
const tasks = ["Enviar Email", "Gerar Relatório", "Corrigir Bug"];

prioritizer.prioritizeTasks(tasks, "Alphabetical");

prioritizer.prioritizeTasks(tasks, "ByLength");
```

💡 **Problemas**:
- As estratégias estão acopladas ao método `prioritizeTasks`.
- Cada nova estratégia exige a modificação do código principal.

### Com o Padrão Strategy

#### 1. Interface Strategy
Define o contrato para as estratégias.

```typescript
interface TaskPriorityStrategy {
    prioritize(tasks: string[]): void;
}
```

#### 2. Estratégias Concretas
Cada estratégia é implementada como uma classe separada.

```typescript
class AlphabeticalPriorityStrategy implements TaskPriorityStrategy {
    prioritize(tasks: string[]): void {
        tasks.sort();
        console.log("Tarefas ordenadas alfabeticamente:", tasks);
    }
}

class LengthPriorityStrategy implements TaskPriorityStrategy {
    prioritize(tasks: string[]): void {
        tasks.sort((a, b) => a.length - b.length);
        console.log("Tarefas ordenadas por tamanho:", tasks);
    }
}
```

#### 3. Contexto
Define a estratégia em tempo de execução.

```typescript
class TaskPriorityManager {
    private strategy?: TaskPriorityStrategy;

    setStrategy(strategy: TaskPriorityStrategy): void {
        this.strategy = strategy;
    }

    applyStrategy(tasks: string[]): void {
        if (this.strategy) {
            this.strategy.prioritize(tasks);
        } else {
            console.log("Nenhuma estratégia definida.");
        }
    }
}
```

#### 4. Exemplo de Uso

```typescript
const priorityManager = new TaskPriorityManager();
const tasks = ["Enviar Email", "Gerar Relatório", "Corrigir Bug"];

priorityManager.setStrategy(new AlphabeticalPriorityStrategy());
priorityManager.applyStrategy(tasks);

priorityManager.setStrategy(new LengthPriorityStrategy());
priorityManager.applyStrategy(tasks);
```

💡 **Benefícios**:
- Estratégias independentes e fáceis de trocar.
- O contexto é flexível e não precisa ser alterado para adicionar novas estratégias.

---

## 4. Uso em APIs Conhecidas 🌐

### **Command** 💾
- **Java Swing**: Listener de eventos como `ActionListener`.
- **Frameworks de Jogos**: Undo/Redo de ações.

### **Strategy** 🔄
- **Java Collections**: Método `sort` com diferentes comparadores.
- **APIs de Autenticação**: Métodos variados como OAuth, JWT.

---

## 5. Refatorações Possíveis 🔄

### Projetos Simples
1. Usar **Command** para gerenciar ações de usuários, como salvar e desfazer mudanças.
2. Aplicar **Strategy** para alternar entre diferentes algoritmos (ex.: ordenação de listas).

### Jogos
1. **Command** para encapsular ações dos personagens (ex.: atacar, defender).
2. **Strategy** para implementar comportamentos de IA.

---

## 6. Pontos Fortes e Fracos ⚖️

### **Command**:
- **Pontos Fortes**:
  - Suporte para **desfazer/refazer** ações. 🔄
  - Reduz o acoplamento entre chamador e receptor.
- **Pontos Fracos**:
  - Pode ser excessivamente complexo para tarefas simples.

### **Strategy**:
- **Pontos Fortes**:
  - Estratégias intercambiáveis dinamicamente. 🔀
  - Código modular e flexível.
- **Pontos Fracos**:
  - Pode ser desnecessário em sistemas com poucas variações.

---

## 7. Conclusões 🎯

Os padrões Command e Strategy aumentam a flexibilidade e a modularidade do código. Embora eficazes, devem ser usados com cuidado para evitar complexidade desnecessária. Aplicações práticas incluem desde sistemas simples até soluções robustas em jogos e APIs corporativas.
