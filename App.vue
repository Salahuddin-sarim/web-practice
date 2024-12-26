<template>
    <div class="flex items-center justify-center min-h-screen p-6 bg-gradient-to-r from-black to-white ">
      <div class="w-full max-w-md p-6 bg-white border rounded-lg shadow">
        <h1 class="mb-4 text-4xl font-bold text-center text-black">To-Do List</h1>

        <div class="flex gap-5 p-8 mb-3 border border-gray-500 rounded-lg ">
            <div class="w-full ">
                <h1>Todo App</h1>
                <p>Keep it up!</p>
                <div class="w-full h-3 mt-5 bg-gray-300 rounded-md ">
                    <div class="w-1/2 h-3 bg-black rounded-md progress"
                    :style="{width: progressWidth}" 
                    ></div>
                </div>
            </div>
            <div class=" stats-number">
                <p class="flex items-center justify-center text-3xl text-white bg-black rounded-full h-28 w-28 numbers">
                 {{completedTasks}} / {{ tasks.length }}</p>
            </div>
    </div>

        <form @submit.prevent="addTask" class="flex mb-4">
          <input
            v-model="newTask"
            type="text"
            placeholder="Add a new task..."
            class="flex-1 p-2 border border-gray-500 rounded-l-lg focus:outline-none "
          />
          <button
            type="submit"
            class="px-4 py-2 text-white bg-black rounded-r-lg "
          >
            Add
          </button>
        </form>
        <ul>
          <li
            v-for="(task, index) in tasks"
            :key="index"
            class="flex items-center justify-between p-2 mb-2 text-black bg-white border border-gray-500 rounded-lg"
          >
            <span :class="{ 'line-through text-black bg-white': task.completed }">
              {{ task.text }}
            </span>
            <div class="flex space-x-2">
              <button
                @click="toggleTask(index)"
                class="p-1 text-white bg-black rounded-lg hover:underline"
              >
                {{ task.completed ? 'Undo' : 'Complete' }}
              </button>
              <button
                @click="openEditModal(index)"
                class="p-1 text-white bg-black rounded-lg hover:underline"
              >
                Edit
              </button>
              <button
                @click="deleteTask(index)"
                class="p-1 text-white bg-black rounded-lg hover:underline"
              >
                Delete
              </button>
            </div>
          </li>
        </ul>
      </div>
  
      <!-- Edit Modal -->
      <div
        v-if="isEditModalOpen"
        class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
      >
        <div class="p-6 bg-white rounded-lg shadow-lg w-96">
          <h2 class="mb-4 text-xl font-bold">Edit Task</h2>
          <input
            v-model="editedTask"
            type="text"
            class="w-full p-2 mb-4 border rounded-lg focus:outline-none "
          />
          <div class="flex justify-end space-x-2">
            <button
              @click="closeEditModal"
              class="px-4 py-2 text-gray-700 bg-gray-300 rounded-lg hover:bg-gray-400"
            >
              Cancel
            </button>
            <button
              @click="saveEdit"
              class="px-4 py-2 text-white bg-black rounded-lg "
            >
              Save
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, watch ,computed} from 'vue';
  
  const tasks = ref([]);
  const newTask = ref('');
  const isEditModalOpen = ref(false);
  const editedTask = ref('');
  const currentEditIndex = ref(null);
  
  // Load tasks from localStorage on mount
  onMounted(() => {
    const savedTasks = localStorage.getItem('tasks');
    if (savedTasks) {
      tasks.value = JSON.parse(savedTasks);
    }
  });
  
  // Watch for changes in tasks and save to localStorage
  watch(
    tasks,
    (newTasks) => {
      localStorage.setItem('tasks', JSON.stringify(newTasks));
    },
    { deep: true }
  );
  //  completedtasks
  const completedTasks = computed(() => tasks.value.filter(task => task.completed).length);
  const progressWidth = computed(() => {
  return tasks.value.length
    ? `${(completedTasks.value / tasks.value.length) * 100}%`
    : '0%';
});
  
  const addTask = () => {
    if (newTask.value.trim() !== '') {
      tasks.value.push({ text: newTask.value.trim(), completed: false });
      newTask.value = '';
    }
  };
  
  const toggleTask = (index) => {
    tasks.value[index].completed = !tasks.value[index].completed;
  };
  
  const deleteTask = (index) => {
    tasks.value.splice(index, 1);
  };
  
  const openEditModal = (index) => {
    currentEditIndex.value = index;
    editedTask.value = tasks.value[index].text;
    isEditModalOpen.value = true;
  };
  
  const closeEditModal = () => {
    isEditModalOpen.value = false;
    editedTask.value = '';
    currentEditIndex.value = null;
  };
  
  const saveEdit = () => {
    if (editedTask.value.trim() !== '' && currentEditIndex.value !== null) {
      tasks.value[currentEditIndex.value].text = editedTask.value.trim();
      closeEditModal();
    }
  };
  </script>
  
  
 
  