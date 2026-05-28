**Code Comprehension**
**Exercise Part:1**

2.
The application is a task management or to-do management system that allows users to create tasks, view task lists, update task statuses, and delete tasks.

These the tevhnologies being used in this app:
  - Node.js
  - JavaScript
  - Commander.js
  - UUID
  - Jest

The main components are:
 - app.js
 - cli.js
 - models.js
 - storage.js

3.
Validate / correct your understanding
What the app is

Your guess is correct:
This is a CLI-based task / to‑do management system that lets you:

Create tasks (with title, description, priority, due date, tags).
List tasks (optionally filtered by status, priority, or overdue).
Update status, priority, due date.
Add/remove tags.
View details of a single task.
Delete tasks.
See statistics (counts by status/priority, overdue, completed last week).
There is no web UI here; everything is done through the command line.

What you were unsure about (“what this app is about”)

The big picture:



4.
- I was clear on my understanding of the application.

- cli.js → Main Application Entry Point
This is the primary startup file for the application.

JSON:
"main": "cli.js"

- cli.js defines the command-line interface (what commands exist, which flags/arguments they take).
- app.js (TaskManager) is the business logic layer that the CLI calls.
- models.js likely defines the Task class and enums (TaskStatus, TaskPriority).
- storage handles saving/loading tasks from disk (likely JSON file).

**Exercise Part:2**

1.
- Based on the codebase there is currently no dedicated export functionality (CSV, Excel, PDF, etc.) implemented yet.

- Yes, the project contains code related to both data transformation and external file operations, mainly inside storage.js and cli.js. The application uses Node.js filesystem APIs such as fs.readFileSync(), fs.writeFileSync(), and fs.existsSync() to read from and write to a local tasks.json file, which handles task persistence. The code also performs several forms of data transformation, including converting JavaScript objects to JSON using JSON.stringify(), parsing JSON back into objects with JSON.parse(), reconstructing raw JSON data into Task class instances, formatting dates with Date objects, filtering collections of tasks, and transforming task data into human-readable CLI output through the formatTask() function. Additionally, methods like getStatistics() aggregate and transform task data into summarized metrics, showing that the project already has a structured data processing flow in place.

- The project contains several reusable utility-style functions that could support future file operations or export features. The formatTask() function in cli.js can be reused for formatting tasks into readable text outputs, while methods in storage.js such as load(), save(), getAllTasks(), and filtering methods provide reusable data retrieval and persistence functionality. The code also uses reusable serialization and transformation patterns like JSON.stringify(), JSON.parse(), date formatting with toISOString(), and task filtering with .filter(), all of which could be extended for exporting or processing task data.


2.
- Based on the initial search, task data export functionality would most likely belong in a new dedicated module such as export.js, utils/exporter.js, or a service layer alongside storage.js. This would fit well with the current layered architecture, where the project already separates CLI handling, business logic, models, and storage responsibilities. Keeping export logic separate would also improve maintainability and scalability.

- Existing components that would likely need modification include cli.js to add export-related commands, app.js to expose export business logic, and storage.js to provide reusable access to task data for exporting. Reusable methods such as getAllTasks(), getTasksByStatus(), getTasksByPriority(), and getStatistics() could support filtering and report generation, while formatTask() in cli.js could assist with text-based formatting.

- Search terms used during the investigation included fs, JSON.stringify, JSON.parse, writeFile, readFile, save, load, filter, formatTask, and toISOString. The main files identified from these searches were storage.js for file operations and persistence logic, cli.js for task formatting and command handling, and app.js for business logic and statistics generation.

- cli.js would likely need modification to add new export commands and command-line options, such as exporting tasks to JSON, CSV, or text files.

- app.js may need updates to include export-related business logic and coordinate task retrieval for exporting.

- Search terms used included fs, JSON.stringify, JSON.parse, writeFile, readFile, save, load, filter, formatTask, toISOString, TaskStorage, and getStatistics.

- More effective search terms would include dueDate, Date(, createdAt, updatedAt, completedAt, setDate, toISOString, isOverdue, markAsDone, updateTaskDueDate, and due. You should also search for .action( in cli.js because Commander.js commands define the entry points for user interactions. A good investigation process would be: start in cli.js to find the command, trace the command into app.js, follow the business logic into models.js and storage.js, then inspect how data is saved into tasks.json. This helps you understand the full feature flow from user input to persistence.

- The export feature would most likely be implemented as a new module such as export.js, utils/exporter.js, or services/exportService.js to keep the architecture clean and separate from the storage and business logic layers. The feature would probably integrate with app.js for task retrieval logic and cli.js for exposing export commands to users. The export module would be responsible for transforming task data into formats such as JSON, CSV, or text files and writing those files to disk using Node.js file system operations.

- Several existing components would likely be affected by this feature. cli.js would need new commands such as export or export-related options and flags. app.js might need new methods to prepare or filter task data before exporting. storage.js would likely be reused because it already handles loading, saving, and retrieving tasks. The formatTask() function in cli.js could potentially be extracted into a reusable utility for formatting exported text output. The tests directory would also need updates to include automated tests for export functionality, file generation, and data formatting behavior.

- My implementation approach would start by identifying the required export formats and where users would trigger the feature from the CLI. Next, I would create a dedicated export module responsible for transforming task data and handling file creation. Then I would connect the export functionality to TaskManager methods in app.js and expose it through new Commander.js commands in cli.js. After that, I would add filtering support so users could export all tasks or filtered subsets such as overdue or completed tasks. Finally, I would create tests to validate file generation, exported content structure, and error handling for invalid paths or unsupported formats.

**Exercise Part:3**

- The core entity classes and domain objects in the application are primarily defined in models.js. The main entity is the Task class, which represents an individual task and contains properties such as id, title, description, priority, status, createdAt, updatedAt, dueDate, completedAt, and tags. The application also defines enum-style configuration objects called TaskStatus and TaskPriority. TaskStatus contains workflow states such as TODO, IN_PROGRESS, REVIEW, and DONE, while TaskPriority defines task urgency levels including LOW, MEDIUM, HIGH, and URGENT.

- Business logic related to tasks is mainly located in app.js inside the TaskManager class. This includes logic for creating tasks, updating statuses and priorities, validating dates, managing tags, deleting tasks, filtering tasks, checking overdue tasks, and generating statistics. Additional task-related behavior is encapsulated inside the Task model itself through methods such as update(), markAsDone(), and isOverdue(). The storage.js file also contains important persistence-related logic for loading, saving, retrieving, and filtering task data.

- Several terminology and workflow concepts appear to be specific to this application. These include task lifecycle states like TODO, IN_PROGRESS, REVIEW, and DONE, as well as priority levels such as LOW, MEDIUM, HIGH, and URGENT. The application also uses concepts like overdue tasks, task tagging, completion tracking, due dates, and weekly completion statistics. CLI-specific terminology such as create, list, status, priority, due, tag, untag, show, and stats reflects how users interact with the system through terminal commands.

- CLI (cli.js)
    ↓
TaskManager (app.js)
    ↓
TaskStorage (storage.js)
    ↓
Task Entity (models.js)
       ├── TaskStatus
       ├── TaskPriority
       ├── dueDate
       ├── tags
       └── timestamps

 - Another way to view the relationships:

TaskManager
    ├── creates Task
    ├── updates Task
    ├── retrieves Task(s)
    └── uses TaskStorage

TaskStorage
    ├── saves Tasks
    ├── loads Tasks
    └── stores Tasks in tasks.json

Task
    ├── has a TaskStatus
    ├── has a TaskPriority
    ├── contains tags
    └── contains date information


 - The Task entity represents a single task or todo item in the application and contains all task-related data such as title, description, status, priority, due dates, tags, and timestamps. TaskStatus represents the workflow state of a task, such as TODO, IN_PROGRESS, REVIEW, or DONE. TaskPriority represents the urgency level of a task, ranging from LOW to URGENT. TaskManager acts as the business logic layer responsible for task operations, validation, filtering, and statistics generation. TaskStorage represents the persistence layer that handles saving and loading tasks from the tasks.json file. The CLI layer in cli.js acts as the user interface that translates terminal commands into application actions.

- Some areas of confusion or questions about the business logic include whether task status transitions are restricted or if any status can change directly to another status. It is also unclear whether priorities should be validated more strictly since the code currently accepts numeric values without strong validation. Another question is whether overdue tasks should automatically change status or trigger notifications in the future. There is also no clear distinction between business rules handled inside the Task model versus rules managed by TaskManager, which could become important as the application grows.

- I am a junior developer trying to understand the domain models and business concepts in a Node.js CLI task management application.

Here is my current understanding of the project:

* The application is a command-line task management or todo system.
* Users can create, list, update, delete, and filter tasks.
* Tasks have statuses, priorities, tags, due dates, and timestamps.
* The project uses Node.js, JavaScript, Commander.js, UUID, and Jest.
* The architecture appears to be layered:

  * `cli.js` handles terminal commands
  * `app.js` contains business logic through `TaskManager`
  * `models.js` defines entities like `Task`, `TaskStatus`, and `TaskPriority`
  * `storage.js` handles persistence using `tasks.json`

Here is my current understanding of the domain model relationships:

* `Task` is the core entity and represents a task/todo item.
* `TaskStatus` defines workflow states such as TODO, IN_PROGRESS, REVIEW, and DONE.
* `TaskPriority` defines urgency levels such as LOW, MEDIUM, HIGH, and URGENT.
* `TaskManager` coordinates business operations such as creating tasks, updating statuses, filtering tasks, and generating statistics.
* `TaskStorage` is responsible for saving and loading task data from JSON storage.

Here are some questions and areas where I am still confused:

1. Should status transitions follow strict workflow rules, or can any status change directly to another status?
2. Which business rules should belong inside the `Task` entity versus inside `TaskManager`?
3. Is `TaskStorage` acting like a repository pattern?
4. Would this architecture scale well if the project moved from JSON storage to a database?
5. Are there any hidden domain concepts or business rules that I may be missing?
6. Is the overdue-task logic considered domain logic or application/service logic?

Please:

* Validate or correct my understanding
* Explain the domain concepts in simpler terms
* Point out any important architectural or business patterns
* Explain how these concepts would evolve in a larger production application
* Identify any missing concepts I should learn about

Then, test my understanding by asking me 5-10 questions about:

* entity relationships
* business logic
* architecture
* data flow
* separation of concerns
* persistence
* task lifecycle behavior

- After answering the AI’s questions, my updated understanding is that the application follows a layered architecture where the CLI layer handles user interaction, TaskManager manages business logic and orchestration, TaskStorage acts similarly to a repository or persistence layer, and the Task entity encapsulates task-related state and behavior. I also understand that some business logic belongs inside the entity itself, such as markAsDone() and isOverdue(), because they directly describe task behavior, while higher-level workflow coordination belongs in TaskManager.
Revised entity relationship diagram:
User (CLI)
    ↓
Commander.js Commands
    ↓
TaskManager (Business Logic Layer)
    ├── creates and updates Tasks
    ├── validates input
    ├── generates statistics
    └── delegates persistence

TaskStorage (Persistence Layer)
    ├── loads tasks from tasks.json
    ├── saves tasks to tasks.json
    ├── filters and retrieves tasks
    └── reconstructs Task objects

Task Entity (Domain Model)
    ├── id
    ├── title
    ├── description
    ├── TaskStatus
    ├── TaskPriority
    ├── dueDate
    ├── completedAt
    ├── createdAt
    ├── updatedAt
    └── tags

TaskStatus
    ├── TODO
    ├── IN_PROGRESS
    ├── REVIEW
    └── DONE

TaskPriority
    ├── LOW
    ├── MEDIUM
    ├── HIGH
    └── URGENT
Glossary of domain terms used in the application:
Term	Meaning
Task	The main domain entity representing a todo or work item
TaskManager	Service layer responsible for business operations and workflow coordination
TaskStorage	Persistence/repository layer responsible for saving and loading task data
TaskStatus	Enum-like object representing the workflow state of a task
TaskPriority	Enum-like object representing task urgency levels
TODO	Initial state for unfinished tasks
IN_PROGRESS	State indicating active work on a task
REVIEW	State indicating a task is awaiting review
DONE	Final completed state for a task
Due Date	Deadline assigned to a task
Overdue Task	A task whose due date has passed and is not completed
Tags	Labels/categories attached to tasks
createdAt	Timestamp showing when the task was created
updatedAt	Timestamp showing when the task was last modified
completedAt	Timestamp showing when the task was completed
Persistence Layer	The layer responsible for storing and retrieving data
Business Logic	Rules and workflows that govern how tasks behave
CLI	Command-line interface used for user interaction
Repository Pattern	Architectural pattern abstracting data storage operations
Serialization	Converting objects into JSON for storage
Deserialization	Reconstructing objects from stored JSON data

-Planning: Based on your understanding from the previous parts:

Identify which files you would need to modify

Outline the changes you would make to implement this rule

Note any questions you would ask your team before implementing

Reflection:

How did the AI prompts help you understand where and how to implement this feature?

What aspects of the codebase are you still unsure about?

What would be your next steps to deepen your understanding?

To implement the new business rule, I would most likely need to modify models.js, app.js, and possibly storage.js and cli.js. The Task entity in models.js would likely need a new status such as ABANDONED added to TaskStatus. The business rule itself would probably belong in app.js inside TaskManager, since this layer already handles task workflows, filtering, and status updates. storage.js may need updates to correctly load and save the new status value, while cli.js may need updates if users should be able to view or manually manage abandoned tasks from the command line.
My implementation approach would start by extending TaskStatus in models.js to include an ABANDONED state. Then I would create a method in either the Task entity or TaskManager to detect tasks that are overdue by more than 7 days and are not marked as HIGH or URGENT priority. I would likely add a helper method such as isAbandonedCandidate() to the Task model because it represents task-specific domain behavior. Next, I would implement a process in TaskManager that automatically updates qualifying tasks to ABANDONED. I would also update filtering, statistics, and CLI display logic so abandoned tasks are included consistently across the application. Finally, I would add automated tests to verify overdue calculations, priority exceptions, and status transitions.

- Before implementing the feature, I would ask the team several questions. Should abandoned tasks be recoverable or reversible? Should high-priority tasks be permanently exempt from abandonment, or only while marked high priority? When and how should the automatic abandonment process run — during application startup, periodically, or only when listing tasks? Should abandoned tasks appear in statistics and filters by default? Are there any existing workflow rules about allowed status transitions that this feature must respect?

- The AI prompts helped me understand the architecture by guiding me to trace the flow between cli.js, app.js, models.js, and storage.js. They made it easier to identify where business rules belong versus where persistence and formatting logic belong. The prompts also helped clarify the role of the Task entity, the service layer (TaskManager), and the repository-like behavior of TaskStorage, which made it easier to reason about where a new business rule should be implemented.

- I am still somewhat unsure about how strict the intended domain rules are, especially around status transitions and ownership of business logic between entities and services. I also do not yet know whether the project is expected to remain a small CLI application or evolve into a larger system with APIs or databases, which could affect implementation choices.

- My next steps would be to explore the tests directory to understand expected behaviors and coding patterns, trace a complete feature flow from CLI command to persistence, and possibly create sequence diagrams for important operations such as task creation and status updates. I would also review Git history or existing issues to understand how the team typically introduces new business rules and architectural changes.
