<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Gestor de Tareas</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-input v-model="newTask" placeholder="Nueva tarea"></ion-input>
      <ion-button @click="addTask">Agregar</ion-button>
      <ion-list>
        <ion-item v-for="task in tasks" :key="task.id">
          <ion-checkbox @click="toggleTask(task)" :checked="task.state === 1"></ion-checkbox>
          <span>{{ task.name }}</span>
          <ion-button color="danger" @click="deleteTask(task.id)">Eliminar</ion-button>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonInput, IonButton, IonList, IonItem, IonCheckbox, IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
import { ref, onMounted } from 'vue';
import { turso } from "@/db/conexion";

const tasks = ref([]);
const newTask = ref('');

const loadTasks = async () => {
  const { rows } = await turso.execute("SELECT * FROM tbl_task");
  tasks.value = rows;
};

const addTask = async () => {
  if (newTask.value.trim()) {
    await turso.execute("INSERT INTO tbl_task (name, state) VALUES (?, ?)", [newTask.value, 1]);
    newTask.value = '';
    loadTasks();
  }
};

const deleteTask = async (id) => {
  await turso.execute("DELETE FROM tbl_task WHERE id = ?", [id]);
  loadTasks();
};

const toggleTask = async (task) => {
  const newState = task.state === 1 ? 0 : 1;
  await turso.execute("UPDATE tbl_task SET state = ? WHERE id = ?", [newState, task.id]);
  loadTasks();
};

onMounted(loadTasks);
</script>

<style scoped>
ion-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

ion-input {
  margin-bottom: 10px;
  width: 90%;
}

ion-button {
  margin-bottom: 10px;
}
</style>