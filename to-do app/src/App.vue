<script setup>
import { ref, onMounted } from "vue";
import Toast from "primevue/toast"; // Import Toast component
import InputText from "primevue/inputtext";
import Button from "primevue/button";
import Dialog from "primevue/dialog";
import Card from "primevue/card";
import SpeedDial from "primevue/speeddial";
import Textarea from "primevue/textarea";

// State Management
const newTask = ref(""); // Holds the value of the new task input
const tasks = ref([]); // Array of tasks
const visible = ref(false); // Controls the visibility of the task input dialog
const toast = ref(null); // Toast reference for future notifications
const isDarkMode = ref(false); // Tracks dark mode preference

// Methods
const addTask = () => {
  if (newTask.value.trim() === "") return; // Prevent adding empty tasks
  tasks.value.push({ text: newTask.value, completed: false }); // Add new task
  newTask.value = ""; // Clear input field
  saveTasksToLocalStorage(); // Save tasks to localStorage
  visible.value = false; // Close dialog
};

const deleteTask = (index) => {
  tasks.value.splice(index, 1); // Remove task by index
  saveTasksToLocalStorage(); // Update localStorage
};

const saveTasksToLocalStorage = () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value)); // Save tasks array to localStorage
};

const loadTasksFromLocalStorage = () => {
  const savedTasks = localStorage.getItem("tasks");
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks); // Load tasks from localStorage if available
  }
};

// Watch for system dark mode preference
onMounted(() => {
  const darkModeQuery = window.matchMedia("(prefers-color-scheme: dark)");
  isDarkMode.value = darkModeQuery.matches; // Set initial dark mode state

  // Listen for changes in the dark mode preference
  darkModeQuery.addEventListener("change", (e) => {
    isDarkMode.value = e.matches;
  });

  // Load tasks from localStorage on component mount
  loadTasksFromLocalStorage();
});
// SpeedDial Items
const items = ref([
  {
    label: 'Add',
    icon: 'pi pi-pencil',
    command: () => {
      toast.value.add({ severity: 'info', summary: 'Add', detail: 'Task Added', life: 3000 });
      visible.value = true; // Open dialog to add a task
    }
  },
  {
    label: 'Delete',
    icon: 'pi pi-trash',
    command: (index) => {
      deleteTask(index);
      toast.value.add({ severity: 'warn', summary: 'Delete', detail: 'Task Deleted', life: 3000 });
    }
  }
]);
</script>

<template>
<div >
  <h1 style=" font-size: 1.6rem; margin-bottom: 1px;">Good Morning, David! </h1>
  <p style="color: #555;  margin-bottom: 1rem;">Today is Tuesday, 12/01/24</p>
</div>

  <div style="display: flex; justify-content: center;margin-top:2rem;">
    <div>
      <div v-if="tasks.length > 0" style="display: flex; flex-direction: column; gap: 1rem; align-items: center; ">
        <div
        
        v-for="(task, index) in tasks" :key="index" style="width: 100%; max-width: 600px; ;border-radius: 20px;">
          <Card 
     
          :class="{ 'custom-card': isDarkMode }"
           :style="{ lineHeight: 1.3 , borderRadius: '0.5rem', boxShadow: '0 4px 6px rgba(0, 0, 0, 0.1)',backgroundColor: isDarkMode ? '#1D2439' : '' ,color:task.completed ? 'gray' : ''} ">
            <template #content>
              <div class="task-container" style="max-width: 100%; padding: 0 1rem;">
                <!-- Top Row: Checkbox and SpeedDial -->
                <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem;">
      
                  <input 
                    type="checkbox" 
                    id="check-24" 
                    name="check" 
                    v-model="task.completed" 
                    class="circular-checkbox"
                  />

      
                  <div style="display: flex; justify-content: flex-end;">
                     <SpeedDial 
                     :buttonProps="{ severity: 'secondary', rounded:false }"
                      :model="items" 
                      direction="left" 
                      :style="{ position: 'relative', background: 'none !important', boxShadow: 'none !important' }"
                      showIcon="pi pi-ellipsis-v"  
                      hideIcon="pi pi-times"
                    />

                  </div>
                </div>
                <span :style="{ textDecoration: task.completed ? 'line-through' : 'none' }">
                  {{ task.text }}
                </span>
              </div>
            </template>
          </Card>
        </div>
      </div>
      <p v-else style="text-align: center; color: gray;">No tasks yet. Click "Create new Task" to add one!</p>
    </div>
    <Button style="position: fixed; bottom: 20px; margin: auto; -webkit-box-shadow:0px 0px 248px 48px rgba(163,66,212,0.18);
-moz-box-shadow: 0px 0px 248px 48px rgba(163,66,212,0.18);
box-shadow: 0px 0px 248px 48px rgba(163,66,212,0.18);" label="Create new Task" rounded @click="visible = true" icon="pi pi-plus" />
  </div>

  <Dialog v-model:visible="visible" modal header="Create Task" :style="{ width: '25rem' }" style="  -webkit-box-shadow:0px 0px 248px 16px rgba(163,66,212,0.3);
-moz-box-shadow: 0px 0px 248px 16px rgba(163,66,212,0.3);
box-shadow: 0px 0px 248px 16px rgba(163,66,212,0.3);">
    <template #header>
      <div><span class="font-bold whitespace-nowrap">New Task</span></div>
    </template>
    <div>
     
      <Textarea v-model="newTask" rows="5" cols="30" autocomplete="off" placeholder="Enter your task"  />
    </div>
    <template #footer>
      <Button label="Cancel" text severity="secondary" @click="visible = false" autofocus />
      <Button label="Save" outlined @click="addTask" autofocus />
    </template>
  </Dialog>
  
  <!-- Toast component -->
  <Toast ref="toast" position="top-right" text severity="secondary"  />
</template>


<style>
ul {
  padding: 0;
}

li {
  list-style: none;
  display: flex;
  align-items: center;
}
</style>


