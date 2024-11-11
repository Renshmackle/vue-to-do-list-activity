<template>
  <v-app>
    <v-container class="todo-container">
      <v-row justify="center">
        <v-col cols="12" md="8">
          <v-card elevation="5" class="pa-4">
            <v-card-title class="d-flex justify-center text-h4 font-weight-bold">
              To-Do List
            </v-card-title>
            <v-card-subtitle class="text-center text-h6">Created by Lawrence Sabrido</v-card-subtitle>

            <v-text-field
              v-model="newTask"
              label="Add a new task"
              @keyup.enter="addTask"
              clearable
              maxlength="30"
              outlined
              color="indigo"
              class="mt-4"
            ></v-text-field>

            <v-btn @click="addTask" color="indigo" class="mb-4" block> Add Task </v-btn>

            <v-divider></v-divider>

            <v-card-subtitle class="mt-4">Added Tasks:</v-card-subtitle>

            <v-list dense>
              <v-list-item-group>
                <v-list-item v-for="(task, index) in tasks" :key="index">
                  <v-list-item-content>
                    <div class="task-item py-5">
                      <span :class="{ completed: task.completed }">{{ task.text }}</span>
                      
                      <!-- Buttons are now below the task name -->
                      <div class="task-buttons">
                        <v-btn icon @click="editTask(index)">
                          <v-icon color="blue">mdi-pencil</v-icon>
                        </v-btn>
                        <v-btn icon @click="deleteTask(index)">
                          <v-icon color="red">mdi-delete</v-icon>
                        </v-btn>
                        <v-btn icon @click="toggleCompletion(index)" :color="task.completed ? 'green' : 'grey'">
                          <v-icon>{{ task.completed ? 'mdi-check-circle' : 'mdi-circle-outline' }}</v-icon>
                        </v-btn>
                      </div>
                    </div>

                    <div class="task-meta">
                      <p>Created: {{ formatTime(task.createdAt) }}</p>
                      <p v-if="task.updatedAt">Updated: {{ formatTime(task.updatedAt) }}</p>
                      <p v-if="task.completedAt">Completed: {{ formatTime(task.completedAt) }}</p>
                    </div>
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-card>
        </v-col>
      </v-row>

      <!-- Edit Task Dialog -->
      <v-dialog v-model="editDialog" max-width="500px">
        <v-card>
          <v-card-title>Edit Task</v-card-title>
          <v-card-text>
            <v-text-field v-model="editTaskText" label="Edit task" outlined></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="blue" text @click="updateTask">Update</v-btn>
            <v-btn color="grey" text @click="closeEditDialog">Cancel</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-container>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      editDialog: false,
      editTaskIndex: null,
      editTaskText: '',
    };
  },
  methods: {
    addTask() {
      if (this.newTask.trim()) {
        const currentTime = new Date();
        this.tasks.push({
          text: this.newTask,
          completed: false,
          createdAt: currentTime,
          updatedAt: null,
          completedAt: null,
        });
        this.newTask = ''; 
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1); 
    },
    
    editTask(index) {
      this.editTaskIndex = index;
      this.editTaskText = this.tasks[index].text;
      this.editDialog = true; 
    },
    updateTask() {
      if (this.editTaskIndex !== null) {
        const task = this.tasks[this.editTaskIndex];
        task.text = this.editTaskText;
        task.updatedAt = new Date(); 
        this.closeEditDialog();
      }
    },
    toggleCompletion(index) {
      const task = this.tasks[index];
      task.completed = !task.completed;
      if (task.completed) {
        task.completedAt = new Date(); 
      } else {
        task.completedAt = null; 
      }
    },
    closeEditDialog() {
      this.editDialog = false;
      this.editTaskIndex = null;
      this.editTaskText = '';
    },
    formatTime(time) {
      return new Date(time).toLocaleString();
    },
  },
};
</script>

<style>
.todo-container {
  max-width: 800px;
  margin: auto;
  padding-top: 40px;
}

.task-item {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.task-buttons {
  display: flex;
  gap: 8px;
  margin-top: 8px; 
}

.task-meta {
  font-size: 0.9em;
  color: #757575;
  margin-top: 5px;
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.v-list-item {
  padding: 10px 0;
}

.v-card {
  background-color: #f9f9f9;
}

.v-btn {
  background-color: #3f51b5 !important;
  color: white !important;
}

.v-btn:hover {
  background-color: #5c6bc0 !important;
}

.v-list-item-content {
  display: flex;
  align-items: center;
}

.v-card-subtitle {
  font-weight: 500;
  color: #757575;
}

.v-divider {
  margin: 10px 0;
}
</style>