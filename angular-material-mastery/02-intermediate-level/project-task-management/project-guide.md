# Advanced Task Management System - Implementation Guide

## 🚀 **Phase 1: Project Foundation & Architecture (Week 1)**

### **Step 1: Project Setup with Modern Angular**

```bash
# Create new Angular project with latest features
ng new task-management-system --routing --style=scss --skip-git
cd task-management-system

# Add Angular Material with custom theme
ng add @angular/material --theme=custom --typography=true --animations=true

# Install additional dependencies
npm install @ngrx/store @ngrx/effects @ngrx/entity @ngrx/devtools
npm install @angular/cdk @angular/flex-layout
npm install chart.js ng2-charts
npm install socket.io-client @types/socket.io-client
npm install date-fns lodash @types/lodash
npm install quill ngx-quill
```

### **Step 2: NgRx Store Architecture Setup**

Create the store structure:
```bash
# Generate NgRx store scaffolding
ng generate @ngrx/schematics:store State --root --module app.module.ts
ng generate @ngrx/schematics:feature store/auth --module app.module.ts
ng generate @ngrx/schematics:feature store/tasks --module app.module.ts
ng generate @ngrx/schematics:feature store/projects --module app.module.ts
ng generate @ngrx/schematics:feature store/users --module app.module.ts
ng generate @ngrx/schematics:feature store/realtime --module app.module.ts
```

### **Step 3: Core Models Definition**

Create `src/app/core/models/task.model.ts`:
```typescript
export interface Task {
  id: string;
  number: number;
  title: string;
  description: string;
  status: TaskStatus;
  priority: TaskPriority;
  type: TaskType;
  projectId: string;
  columnId: string;
  assignees: User[];
  reporter: User;
  labels: Label[];
  dueDate: Date | null;
  estimatedHours: number | null;
  loggedHours: number;
  subtasks: Subtask[];
  comments: Comment[];
  attachments: Attachment[];
  customFields: CustomField[];
  position: number;
  createdAt: Date;
  updatedAt: Date;
  completedAt: Date | null;
  locked: boolean;
  watchers: User[];
  dependencies: TaskDependency[];
  parentTaskId: string | null;
  sprintId: string | null;
  storyPoints: number | null;
}

export enum TaskStatus {
  TODO = 'TODO',
  IN_PROGRESS = 'IN_PROGRESS',
  IN_REVIEW = 'IN_REVIEW',
  TESTING = 'TESTING',
  DONE = 'DONE',
  BLOCKED = 'BLOCKED',
  CANCELLED = 'CANCELLED'
}

export enum TaskPriority {
  LOWEST = 'LOWEST',
  LOW = 'LOW',
  MEDIUM = 'MEDIUM',
  HIGH = 'HIGH',
  HIGHEST = 'HIGHEST',
  CRITICAL = 'CRITICAL'
}

export enum TaskType {
  STORY = 'STORY',
  TASK = 'TASK',
  BUG = 'BUG',
  EPIC = 'EPIC',
  SUBTASK = 'SUBTASK'
}

export interface Subtask {
  id: string;
  title: string;
  completed: boolean;
  assignee: User | null;
  dueDate: Date | null;
  createdAt: Date;
  updatedAt: Date;
}

export interface Comment {
  id: string;
  author: User;
  content: string;
  mentions: User[];
  reactions: Reaction[];
  edited: boolean;
  createdAt: Date;
  updatedAt: Date;
  parentCommentId: string | null;
  attachments: Attachment[];
}

export interface Attachment {
  id: string;
  filename: string;
  originalName: string;
  mimeType: string;
  size: number;
  url: string;
  thumbnailUrl: string | null;
  uploadedBy: User;
  uploadedAt: Date;
}

export interface Label {
  id: string;
  name: string;
  color: string;
  description: string;
  projectId: string;
}

export interface CustomField {
  id: string;
  fieldId: string;
  value: any;
  fieldType: CustomFieldType;
}

export enum CustomFieldType {
  TEXT = 'TEXT',
  NUMBER = 'NUMBER',
  DATE = 'DATE',
  SELECT = 'SELECT',
  MULTI_SELECT = 'MULTI_SELECT',
  CHECKBOX = 'CHECKBOX',
  URL = 'URL'
}

export interface TaskDependency {
  id: string;
  dependentTaskId: string;
  blockedTaskId: string;
  type: DependencyType;
  createdAt: Date;
}

export enum DependencyType {
  BLOCKS = 'BLOCKS',
  CLONES = 'CLONES',
  DUPLICATES = 'DUPLICATES',
  RELATES = 'RELATES'
}

export interface TaskFilters {
  search: string;
  assignees: string[];
  reporters: string[];
  labels: string[];
  priorities: TaskPriority[];
  types: TaskType[];
  statuses: TaskStatus[];
  dueDateRange: DateRange | null;
  createdDateRange: DateRange | null;
  customFields: CustomFieldFilter[];
}

export interface TaskSorting {
  field: TaskSortField;
  direction: 'asc' | 'desc';
}

export enum TaskSortField {
  CREATED = 'created',
  UPDATED = 'updated',
  DUE_DATE = 'dueDate',
  PRIORITY = 'priority',
  ASSIGNEE = 'assignee',
  TITLE = 'title',
  STATUS = 'status'
}

export interface DateRange {
  start: Date;
  end: Date;
}

export interface CustomFieldFilter {
  fieldId: string;
  operator: FilterOperator;
  value: any;
}

export enum FilterOperator {
  EQUALS = 'EQUALS',
  NOT_EQUALS = 'NOT_EQUALS',
  CONTAINS = 'CONTAINS',
  NOT_CONTAINS = 'NOT_CONTAINS',
  GREATER_THAN = 'GREATER_THAN',
  LESS_THAN = 'LESS_THAN',
  IS_EMPTY = 'IS_EMPTY',
  IS_NOT_EMPTY = 'IS_NOT_EMPTY'
}
```

Create `src/app/core/models/project.model.ts`:
```typescript
export interface Project {
  id: string;
  key: string;
  name: string;
  description: string;
  avatar: string | null;
  category: ProjectCategory;
  type: ProjectType;
  lead: User;
  members: ProjectMember[];
  columns: KanbanColumn[];
  workflows: Workflow[];
  labels: Label[];
  customFields: ProjectCustomField[];
  settings: ProjectSettings;
  statistics: ProjectStatistics;
  createdAt: Date;
  updatedAt: Date;
  archivedAt: Date | null;
}

export enum ProjectCategory {
  SOFTWARE = 'SOFTWARE',
  MARKETING = 'MARKETING',
  HR = 'HR',
  SALES = 'SALES',
  OPERATIONS = 'OPERATIONS',
  OTHER = 'OTHER'
}

export enum ProjectType {
  KANBAN = 'KANBAN',
  SCRUM = 'SCRUM',
  TEMPLATE = 'TEMPLATE'
}

export interface ProjectMember {
  userId: string;
  user: User;
  role: ProjectRole;
  joinedAt: Date;
  permissions: ProjectPermission[];
}

export enum ProjectRole {
  ADMIN = 'ADMIN',
  DEVELOPER = 'DEVELOPER',
  VIEWER = 'VIEWER'
}

export interface KanbanColumn {
  id: string;
  name: string;
  description: string;
  color: string;
  position: number;
  wipLimit: number | null;
  statuses: TaskStatus[];
  isCollapsed: boolean;
}

export interface Workflow {
  id: string;
  name: string;
  description: string;
  statuses: WorkflowStatus[];
  transitions: WorkflowTransition[];
  isDefault: boolean;
}

export interface WorkflowStatus {
  id: string;
  name: string;
  category: StatusCategory;
  color: string;
}

export enum StatusCategory {
  TODO = 'TODO',
  IN_PROGRESS = 'IN_PROGRESS',
  DONE = 'DONE'
}

export interface WorkflowTransition {
  id: string;
  name: string;
  fromStatusId: string;
  toStatusId: string;
  conditions: TransitionCondition[];
  actions: TransitionAction[];
}

export interface ProjectSettings {
  timeTracking: boolean;
  estimation: boolean;
  subtasks: boolean;
  attachments: boolean;
  comments: boolean;
  workflowId: string;
  defaultAssignee: string | null;
  issueTypes: TaskType[];
  priorities: TaskPriority[];
}

export interface ProjectStatistics {
  totalTasks: number;
  completedTasks: number;
  overdueTasks: number;
  averageCompletionTime: number;
  burndownData: BurndownPoint[];
  velocityData: VelocityPoint[];
}

export interface BurndownPoint {
  date: Date;
  ideal: number;
  actual: number;
}

export interface VelocityPoint {
  sprint: string;
  committed: number;
  completed: number;
}
```

### **Step 4: NgRx State Management Implementation**

Create `src/app/store/tasks/tasks.state.ts`:
```typescript
import { EntityState, EntityAdapter, createEntityAdapter } from '@ngrx/entity';
import { Task, TaskFilters, TaskSorting } from '../../core/models/task.model';

export interface TasksState extends EntityState<Task> {
  selectedTaskId: string | null;
  filters: TaskFilters;
  sorting: TaskSorting;
  searchTerm: string;
  loading: boolean;
  error: string | null;
  
  // Kanban specific state
  kanbanColumns: { [columnId: string]: string[] }; // columnId -> taskIds
  dragState: DragState;
  
  // Bulk operations
  selectedTaskIds: string[];
  bulkOperationInProgress: boolean;
  
  // Real-time state
  onlineUsers: string[];
  taskLocks: { [taskId: string]: TaskLock };
  
  // Pagination
  currentPage: number;
  pageSize: number;
  totalCount: number;
  
  // View state
  viewMode: TaskViewMode;
  groupBy: TaskGroupBy;
  compactMode: boolean;
}

export interface DragState {
  isDragging: boolean;
  draggedTaskId: string | null;
  draggedFromColumn: string | null;
  dragOverColumn: string | null;
}

export interface TaskLock {
  userId: string;
  userName: string;
  lockedAt: Date;
  expiresAt: Date;
}

export enum TaskViewMode {
  KANBAN = 'KANBAN',
  LIST = 'LIST',
  CALENDAR = 'CALENDAR',
  TIMELINE = 'TIMELINE'
}

export enum TaskGroupBy {
  NONE = 'NONE',
  ASSIGNEE = 'ASSIGNEE',
  PRIORITY = 'PRIORITY',
  LABEL = 'LABEL',
  DUE_DATE = 'DUE_DATE',
  STATUS = 'STATUS'
}

export const tasksAdapter: EntityAdapter<Task> = createEntityAdapter<Task>({
  selectId: (task: Task) => task.id,
  sortComparer: false // We'll handle sorting in selectors
});

export const initialTasksState: TasksState = tasksAdapter.getInitialState({
  selectedTaskId: null,
  filters: {
    search: '',
    assignees: [],
    reporters: [],
    labels: [],
    priorities: [],
    types: [],
    statuses: [],
    dueDateRange: null,
    createdDateRange: null,
    customFields: []
  },
  sorting: {
    field: TaskSortField.UPDATED,
    direction: 'desc'
  },
  searchTerm: '',
  loading: false,
  error: null,
  kanbanColumns: {},
  dragState: {
    isDragging: false,
    draggedTaskId: null,
    draggedFromColumn: null,
    dragOverColumn: null
  },
  selectedTaskIds: [],
  bulkOperationInProgress: false,
  onlineUsers: [],
  taskLocks: {},
  currentPage: 1,
  pageSize: 50,
  totalCount: 0,
  viewMode: TaskViewMode.KANBAN,
  groupBy: TaskGroupBy.NONE,
  compactMode: false
});
```

Create `src/app/store/tasks/tasks.actions.ts`:
```typescript
import { createActionGroup, emptyProps, props } from '@ngrx/store';
import { Task, TaskFilters, TaskSorting, TaskViewMode, TaskGroupBy } from '../../core/models/task.model';
import { Update } from '@ngrx/entity';

export const TasksActions = createActionGroup({
  source: 'Tasks',
  events: {
    // Loading tasks
    'Load Tasks': props<{ projectId: string; filters?: Partial<TaskFilters> }>(),
    'Load Tasks Success': props<{ tasks: Task[]; totalCount: number }>(),
    'Load Tasks Failure': props<{ error: string }>(),
    
    // Single task operations
    'Load Task': props<{ taskId: string }>(),
    'Load Task Success': props<{ task: Task }>(),
    'Load Task Failure': props<{ error: string }>(),
    
    'Create Task': props<{ task: Partial<Task> }>(),
    'Create Task Success': props<{ task: Task }>(),
    'Create Task Failure': props<{ error: string }>(),
    
    'Update Task': props<{ taskId: string; changes: Partial<Task> }>(),
    'Update Task Success': props<{ update: Update<Task> }>(),
    'Update Task Failure': props<{ error: string }>(),
    
    'Delete Task': props<{ taskId: string }>(),
    'Delete Task Success': props<{ taskId: string }>(),
    'Delete Task Failure': props<{ error: string }>(),
    
    // Bulk operations
    'Select Task': props<{ taskId: string }>(),
    'Select Multiple Tasks': props<{ taskIds: string[] }>(),
    'Deselect Task': props<{ taskId: string }>(),
    'Clear Selection': emptyProps(),
    
    'Bulk Update Tasks': props<{ taskIds: string[]; changes: Partial<Task> }>(),
    'Bulk Update Tasks Success': props<{ updates: Update<Task>[] }>(),
    'Bulk Update Tasks Failure': props<{ error: string }>(),
    
    'Bulk Delete Tasks': props<{ taskIds: string[] }>(),
    'Bulk Delete Tasks Success': props<{ taskIds: string[] }>(),
    'Bulk Delete Tasks Failure': props<{ error: string }>(),
    
    // Kanban operations
    'Load Kanban Columns': props<{ projectId: string }>(),
    'Load Kanban Columns Success': props<{ columns: { [columnId: string]: string[] } }>(),
    
    'Move Task': props<{ taskId: string; fromColumnId: string; toColumnId: string; previousIndex: number; currentIndex: number }>(),
    'Move Task Success': props<{ taskId: string; fromColumnId: string; toColumnId: string; position: number }>(),
    'Move Task Failure': props<{ error: string }>(),
    
    'Reorder Tasks': props<{ columnId: string; previousIndex: number; currentIndex: number }>(),
    'Reorder Tasks Success': props<{ columnId: string; taskIds: string[] }>(),
    
    // Drag and drop state
    'Start Drag': props<{ taskId: string; columnId: string }>(),
    'End Drag': emptyProps(),
    'Drag Over Column': props<{ columnId: string }>(),
    'Drag Leave Column': emptyProps(),
    
    // Filtering and sorting
    'Update Filters': props<{ filters: Partial<TaskFilters> }>(),
    'Clear Filters': emptyProps(),
    'Update Sorting': props<{ sorting: TaskSorting }>(),
    'Update Search Term': props<{ searchTerm: string }>(),
    
    // View mode
    'Change View Mode': props<{ viewMode: TaskViewMode }>(),
    'Change Group By': props<{ groupBy: TaskGroupBy }>(),
    'Toggle Compact Mode': emptyProps(),
    
    // Pagination
    'Change Page': props<{ page: number }>(),
    'Change Page Size': props<{ pageSize: number }>(),
    
    // Real-time updates
    'Task Updated Realtime': props<{ task: Task; userId: string }>(),
    'Task Moved Realtime': props<{ taskId: string; fromColumn: string; toColumn: string; userId: string }>(),
    'Task Locked': props<{ taskId: string; userId: string; userName: string }>(),
    'Task Unlocked': props<{ taskId: string }>(),
    'User Online': props<{ userId: string }>(),
    'User Offline': props<{ userId: string }>(),
    
    // Comments
    'Add Comment': props<{ taskId: string; comment: string; mentions?: string[] }>(),
    'Add Comment Success': props<{ taskId: string; comment: Comment }>(),
    'Add Comment Failure': props<{ error: string }>(),
    
    'Update Comment': props<{ taskId: string; commentId: string; content: string }>(),
    'Delete Comment': props<{ taskId: string; commentId: string }>(),
    
    // Attachments
    'Upload Attachment': props<{ taskId: string; file: File }>(),
    'Upload Attachment Success': props<{ taskId: string; attachment: Attachment }>(),
    'Upload Attachment Failure': props<{ error: string }>(),
    
    'Delete Attachment': props<{ taskId: string; attachmentId: string }>(),
    'Delete Attachment Success': props<{ taskId: string; attachmentId: string }>(),
    
    // Time tracking
    'Start Time Tracking': props<{ taskId: string }>(),
    'Stop Time Tracking': props<{ taskId: string; timeSpent: number }>(),
    'Log Time': props<{ taskId: string; timeSpent: number; description?: string }>(),
    
    // Subtasks
    'Add Subtask': props<{ taskId: string; subtask: Partial<Subtask> }>(),
    'Update Subtask': props<{ taskId: string; subtaskId: string; changes: Partial<Subtask> }>(),
    'Delete Subtask': props<{ taskId: string; subtaskId: string }>(),
    'Toggle Subtask': props<{ taskId: string; subtaskId: string }>(),
  }
});
```

Create `src/app/store/tasks/tasks.reducer.ts`:
```typescript
import { createReducer, on } from '@ngrx/store';
import { TasksActions } from './tasks.actions';
import { tasksAdapter, initialTasksState, TasksState } from './tasks.state';

export const tasksReducer = createReducer(
  initialTasksState,
  
  // Loading tasks
  on(TasksActions.loadTasks, (state): TasksState => ({
    ...state,
    loading: true,
    error: null
  })),
  
  on(TasksActions.loadTasksSuccess, (state, { tasks, totalCount }): TasksState => 
    tasksAdapter.setAll(tasks, {
      ...state,
      loading: false,
      error: null,
      totalCount
    })
  ),
  
  on(TasksActions.loadTasksFailure, (state, { error }): TasksState => ({
    ...state,
    loading: false,
    error
  })),

  // Single task operations
  on(TasksActions.loadTaskSuccess, (state, { task }): TasksState =>
    tasksAdapter.upsertOne(task, state)
  ),

  on(TasksActions.createTaskSuccess, (state, { task }): TasksState =>
    tasksAdapter.addOne(task, {
      ...state,
      totalCount: state.totalCount + 1
    })
  ),

  on(TasksActions.updateTaskSuccess, (state, { update }): TasksState =>
    tasksAdapter.updateOne(update, state)
  ),

  on(TasksActions.deleteTaskSuccess, (state, { taskId }): TasksState =>
    tasksAdapter.removeOne(taskId, {
      ...state,
      totalCount: state.totalCount - 1,
      selectedTaskIds: state.selectedTaskIds.filter(id => id !== taskId)
    })
  ),

  // Selection
  on(TasksActions.selectTask, (state, { taskId }): TasksState => ({
    ...state,
    selectedTaskId: taskId
  })),

  on(TasksActions.selectMultipleTasks, (state, { taskIds }): TasksState => ({
    ...state,
    selectedTaskIds: [...new Set([...state.selectedTaskIds, ...taskIds])]
  })),

  on(TasksActions.deselectTask, (state, { taskId }): TasksState => ({
    ...state,
    selectedTaskIds: state.selectedTaskIds.filter(id => id !== taskId)
  })),

  on(TasksActions.clearSelection, (state): TasksState => ({
    ...state,
    selectedTaskIds: [],
    selectedTaskId: null
  })),

  // Bulk operations
  on(TasksActions.bulkUpdateTasks, (state): TasksState => ({
    ...state,
    bulkOperationInProgress: true
  })),

  on(TasksActions.bulkUpdateTasksSuccess, (state, { updates }): TasksState =>
    tasksAdapter.updateMany(updates, {
      ...state,
      bulkOperationInProgress: false
    })
  ),

  on(TasksActions.bulkUpdateTasksFailure, (state, { error }): TasksState => ({
    ...state,
    bulkOperationInProgress: false,
    error
  })),

  // Kanban operations
  on(TasksActions.loadKanbanColumnsSuccess, (state, { columns }): TasksState => ({
    ...state,
    kanbanColumns: columns
  })),

  on(TasksActions.moveTaskSuccess, (state, { taskId, fromColumnId, toColumnId, position }): TasksState => {
    const newKanbanColumns = { ...state.kanbanColumns };
    
    // Remove from old column
    if (newKanbanColumns[fromColumnId]) {
      newKanbanColumns[fromColumnId] = newKanbanColumns[fromColumnId].filter(id => id !== taskId);
    }
    
    // Add to new column at specific position
    if (!newKanbanColumns[toColumnId]) {
      newKanbanColumns[toColumnId] = [];
    }
    newKanbanColumns[toColumnId].splice(position, 0, taskId);
    
    return {
      ...state,
      kanbanColumns: newKanbanColumns
    };
  }),

  // Drag state
  on(TasksActions.startDrag, (state, { taskId, columnId }): TasksState => ({
    ...state,
    dragState: {
      isDragging: true,
      draggedTaskId: taskId,
      draggedFromColumn: columnId,
      dragOverColumn: null
    }
  })),

  on(TasksActions.endDrag, (state): TasksState => ({
    ...state,
    dragState: {
      isDragging: false,
      draggedTaskId: null,
      draggedFromColumn: null,
      dragOverColumn: null
    }
  })),

  on(TasksActions.dragOverColumn, (state, { columnId }): TasksState => ({
    ...state,
    dragState: {
      ...state.dragState,
      dragOverColumn: columnId
    }
  })),

  // Filters and sorting
  on(TasksActions.updateFilters, (state, { filters }): TasksState => ({
    ...state,
    filters: { ...state.filters, ...filters },
    currentPage: 1 // Reset to first page when filtering
  })),

  on(TasksActions.clearFilters, (state): TasksState => ({
    ...state,
    filters: initialTasksState.filters,
    searchTerm: '',
    currentPage: 1
  })),

  on(TasksActions.updateSorting, (state, { sorting }): TasksState => ({
    ...state,
    sorting
  })),

  on(TasksActions.updateSearchTerm, (state, { searchTerm }): TasksState => ({
    ...state,
    searchTerm,
    currentPage: 1 // Reset to first page when searching
  })),

  // View mode
  on(TasksActions.changeViewMode, (state, { viewMode }): TasksState => ({
    ...state,
    viewMode
  })),

  on(TasksActions.changeGroupBy, (state, { groupBy }): TasksState => ({
    ...state,
    groupBy
  })),

  on(TasksActions.toggleCompactMode, (state): TasksState => ({
    ...state,
    compactMode: !state.compactMode
  })),

  // Pagination
  on(TasksActions.changePage, (state, { page }): TasksState => ({
    ...state,
    currentPage: page
  })),

  on(TasksActions.changePageSize, (state, { pageSize }): TasksState => ({
    ...state,
    pageSize,
    currentPage: 1 // Reset to first page when changing page size
  })),

  // Real-time updates
  on(TasksActions.taskUpdatedRealtime, (state, { task }): TasksState =>
    tasksAdapter.upsertOne(task, state)
  ),

  on(TasksActions.taskLocked, (state, { taskId, userId, userName }): TasksState => ({
    ...state,
    taskLocks: {
      ...state.taskLocks,
      [taskId]: {
        userId,
        userName,
        lockedAt: new Date(),
        expiresAt: new Date(Date.now() + 5 * 60 * 1000) // 5 minutes
      }
    }
  })),

  on(TasksActions.taskUnlocked, (state, { taskId }): TasksState => {
    const newTaskLocks = { ...state.taskLocks };
    delete newTaskLocks[taskId];
    return {
      ...state,
      taskLocks: newTaskLocks
    };
  }),

  on(TasksActions.userOnline, (state, { userId }): TasksState => ({
    ...state,
    onlineUsers: [...new Set([...state.onlineUsers, userId])]
  })),

  on(TasksActions.userOffline, (state, { userId }): TasksState => ({
    ...state,
    onlineUsers: state.onlineUsers.filter(id => id !== userId)
  }))
);
```

### **Step 5: Advanced Selectors with Memoization**

Create `src/app/store/tasks/tasks.selectors.ts`:
```typescript
import { createFeatureSelector, createSelector } from '@ngrx/store';
import { tasksAdapter, TasksState, TaskViewMode, TaskGroupBy } from './tasks.state';
import { Task, TaskPriority, TaskStatus } from '../../core/models/task.model';

export const selectTasksState = createFeatureSelector<TasksState>('tasks');

const { selectIds, selectEntities, selectAll, selectTotal } = tasksAdapter.getSelectors();

// Basic selectors
export const selectAllTasks = createSelector(selectTasksState, selectAll);
export const selectTaskEntities = createSelector(selectTasksState, selectEntities);
export const selectTaskIds = createSelector(selectTasksState, selectIds);
export const selectTotalTasks = createSelector(selectTasksState, selectTotal);

export const selectTasksLoading = createSelector(
  selectTasksState,
  (state) => state.loading
);

export const selectTasksError = createSelector(
  selectTasksState,
  (state) => state.error
);

// Selection selectors
export const selectSelectedTaskId = createSelector(
  selectTasksState,
  (state) => state.selectedTaskId
);

export const selectSelectedTask = createSelector(
  selectTaskEntities,
  selectSelectedTaskId,
  (entities, selectedId) => selectedId ? entities[selectedId] : null
);

export const selectSelectedTaskIds = createSelector(
  selectTasksState,
  (state) => state.selectedTaskIds
);

export const selectSelectedTasks = createSelector(
  selectTaskEntities,
  selectSelectedTaskIds,
  (entities, selectedIds) => selectedIds.map(id => entities[id]).filter(Boolean) as Task[]
);

// Filter and search selectors
export const selectTaskFilters = createSelector(
  selectTasksState,
  (state) => state.filters
);

export const selectSearchTerm = createSelector(
  selectTasksState,
  (state) => state.searchTerm
);

export const selectTaskSorting = createSelector(
  selectTasksState,
  (state) => state.sorting
);

// Filtered and sorted tasks
export const selectFilteredTasks = createSelector(
  selectAllTasks,
  selectTaskFilters,
  selectSearchTerm,
  (tasks, filters, searchTerm) => {
    let filteredTasks = tasks;

    // Apply search filter
    if (searchTerm) {
      const searchLower = searchTerm.toLowerCase();
      filteredTasks = filteredTasks.filter(task =>
        task.title.toLowerCase().includes(searchLower) ||
        task.description.toLowerCase().includes(searchLower) ||
        task.number.toString().includes(searchTerm) ||
        task.assignees.some(assignee => 
          assignee.name.toLowerCase().includes(searchLower) ||
          assignee.email.toLowerCase().includes(searchLower)
        )
      );
    }

    // Apply assignee filter
    if (filters.assignees.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        task.assignees.some(assignee => filters.assignees.includes(assignee.id))
      );
    }

    // Apply reporter filter
    if (filters.reporters.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        filters.reporters.includes(task.reporter.id)
      );
    }

    // Apply label filter
    if (filters.labels.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        task.labels.some(label => filters.labels.includes(label.id))
      );
    }

    // Apply priority filter
    if (filters.priorities.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        filters.priorities.includes(task.priority)
      );
    }

    // Apply type filter
    if (filters.types.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        filters.types.includes(task.type)
      );
    }

    // Apply status filter
    if (filters.statuses.length > 0) {
      filteredTasks = filteredTasks.filter(task =>
        filters.statuses.includes(task.status)
      );
    }

    // Apply due date filter
    if (filters.dueDateRange) {
      filteredTasks = filteredTasks.filter(task => {
        if (!task.dueDate) return false;
        const dueDate = new Date(task.dueDate);
        return dueDate >= filters.dueDateRange!.start && dueDate <= filters.dueDateRange!.end;
      });
    }

    // Apply created date filter
    if (filters.createdDateRange) {
      filteredTasks = filteredTasks.filter(task => {
        const createdDate = new Date(task.createdAt);
        return createdDate >= filters.createdDateRange!.start && createdDate <= filters.createdDateRange!.end;
      });
    }

    return filteredTasks;
  }
);

export const selectSortedTasks = createSelector(
  selectFilteredTasks,
  selectTaskSorting,
  (tasks, sorting) => {
    const sortedTasks = [...tasks];
    
    sortedTasks.sort((a, b) => {
      let comparison = 0;
      
      switch (sorting.field) {
        case 'title':
          comparison = a.title.localeCompare(b.title);
          break;
        case 'created':
          comparison = new Date(a.createdAt).getTime() - new Date(b.createdAt).getTime();
          break;
        case 'updated':
          comparison = new Date(a.updatedAt).getTime() - new Date(b.updatedAt).getTime();
          break;
        case 'dueDate':
          if (!a.dueDate && !b.dueDate) comparison = 0;
          else if (!a.dueDate) comparison = 1;
          else if (!b.dueDate) comparison = -1;
          else comparison = new Date(a.dueDate).getTime() - new Date(b.dueDate).getTime();
          break;
        case 'priority':
          const priorityOrder = { 
            CRITICAL: 6, HIGHEST: 5, HIGH: 4, 
            MEDIUM: 3, LOW: 2, LOWEST: 1 
          };
          comparison = priorityOrder[a.priority] - priorityOrder[b.priority];
          break;
        case 'assignee':
          const aAssignee = a.assignees[0]?.name || '';
          const bAssignee = b.assignees[0]?.name || '';
          comparison = aAssignee.localeCompare(bAssignee);
          break;
        case 'status':
          comparison = a.status.localeCompare(b.status);
          break;
        default:
          comparison = 0;
      }
      
      return sorting.direction === 'desc' ? -comparison : comparison;
    });
    
    return sortedTasks;
  }
);

// Kanban selectors
export const selectKanbanColumns = createSelector(
  selectTasksState,
  (state) => state.kanbanColumns
);

export const selectDragState = createSelector(
  selectTasksState,
  (state) => state.dragState
);

export const selectDraggingTaskId = createSelector(
  selectDragState,
  (dragState) => dragState.draggedTaskId
);

export const selectTasksByColumn = createSelector(
  selectTaskEntities,
  selectKanbanColumns,
  (entities, columns) => {
    const columnTasks: { [columnId: string]: Task[] } = {};
    
    Object.entries(columns).forEach(([columnId, taskIds]) => {
      columnTasks[columnId] = taskIds
        .map(id => entities[id])
        .filter(Boolean) as Task[];
    });
    
    return columnTasks;
  }
);

// Grouped tasks selectors
export const selectGroupBy = createSelector(
  selectTasksState,
  (state) => state.groupBy
);

export const selectGroupedTasks = createSelector(
  selectSortedTasks,
  selectGroupBy,
  (tasks, groupBy) => {
    if (groupBy === TaskGroupBy.NONE) {
      return { 'All Tasks': tasks };
    }

    const grouped: { [key: string]: Task[] } = {};

    tasks.forEach(task => {
      let groupKey: string;

      switch (groupBy) {
        case TaskGroupBy.ASSIGNEE:
          groupKey = task.assignees.length > 0 
            ? task.assignees[0].name 
            : 'Unassigned';
          break;
        case TaskGroupBy.PRIORITY:
          groupKey = task.priority;
          break;
        case TaskGroupBy.STATUS:
          groupKey = task.status;
          break;
        case TaskGroupBy.LABEL:
          groupKey = task.labels.length > 0 
            ? task.labels[0].name 
            : 'No Label';
          break;
        case TaskGroupBy.DUE_DATE:
          if (!task.dueDate) {
            groupKey = 'No Due Date';
          } else {
            const dueDate = new Date(task.dueDate);
            const today = new Date();
            const tomorrow = new Date(today);
            tomorrow.setDate(today.getDate() + 1);
            
            if (dueDate.toDateString() === today.toDateString()) {
              groupKey = 'Due Today';
            } else if (dueDate.toDateString() === tomorrow.toDateString()) {
              groupKey = 'Due Tomorrow';
            } else if (dueDate < today) {
              groupKey = 'Overdue';
            } else {
              groupKey = 'Future';
            }
          }
          break;
        default:
          groupKey = 'All Tasks';
      }

      if (!grouped[groupKey]) {
        grouped[groupKey] = [];
      }
      grouped[groupKey].push(task);
    });

    return grouped;
  }
);

// View mode selectors
export const selectViewMode = createSelector(
  selectTasksState,
  (state) => state.viewMode
);

export const selectCompactMode = createSelector(
  selectTasksState,
  (state) => state.compactMode
);

// Pagination selectors
export const selectPagination = createSelector(
  selectTasksState,
  (state) => ({
    currentPage: state.currentPage,
    pageSize: state.pageSize,
    totalCount: state.totalCount,
    totalPages: Math.ceil(state.totalCount / state.pageSize)
  })
);

export const selectPaginatedTasks = createSelector(
  selectSortedTasks,
  selectPagination,
  (tasks, pagination) => {
    const startIndex = (pagination.currentPage - 1) * pagination.pageSize;
    const endIndex = startIndex + pagination.pageSize;
    return tasks.slice(startIndex, endIndex);
  }
);

// Statistics selectors
export const selectTaskStatistics = createSelector(
  selectAllTasks,
  (tasks) => {
    const total = tasks.length;
    const completed = tasks.filter(task => task.status === TaskStatus.DONE).length;
    const inProgress = tasks.filter(task => task.status === TaskStatus.IN_PROGRESS).length;
    const overdue = tasks.filter(task => {
      if (!task.dueDate) return false;
      return new Date(task.dueDate) < new Date() && task.status !== TaskStatus.DONE;
    }).length;
    
    const priorityCount = tasks.reduce((acc, task) => {
      acc[task.priority] = (acc[task.priority] || 0) + 1;
      return acc;
    }, {} as Record<TaskPriority, number>);

    const statusCount = tasks.reduce((acc, task) => {
      acc[task.status] = (acc[task.status] || 0) + 1;
      return acc;
    }, {} as Record<TaskStatus, number>);

    return {
      total,
      completed,
      inProgress,
      overdue,
      completionRate: total > 0 ? (completed / total) * 100 : 0,
      priorityCount,
      statusCount
    };
  }
);

// Real-time selectors
export const selectOnlineUsers = createSelector(
  selectTasksState,
  (state) => state.onlineUsers
);

export const selectTaskLocks = createSelector(
  selectTasksState,
  (state) => state.taskLocks
);

export const selectTaskLock = (taskId: string) => createSelector(
  selectTaskLocks,
  (locks) => locks[taskId]
);

// Bulk operations selectors
export const selectBulkOperationInProgress = createSelector(
  selectTasksState,
  (state) => state.bulkOperationInProgress
);

export const selectHasSelectedTasks = createSelector(
  selectSelectedTaskIds,
  (selectedIds) => selectedIds.length > 0
);

export const selectSelectedTasksCount = createSelector(
  selectSelectedTaskIds,
  (selectedIds) => selectedIds.length
);
```

This implementation guide provides the foundation for building an enterprise-grade task management system. The architecture includes advanced NgRx patterns, comprehensive state management, and real-time collaboration features.

The next sections would continue with Effects implementation, UI components, real-time services, and testing strategies. This foundation demonstrates the complexity and sophistication expected at the intermediate level.

Continue to the next part for Effects, Services, and UI implementation?
