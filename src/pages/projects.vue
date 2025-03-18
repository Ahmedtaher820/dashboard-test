<template>
    <v-sheet border rounded width="100%" class="me-0 pa-3">
      <v-text-field
        v-model="search"
        label="Search By Name"
        class="my-5 mx-3"
        hide-details
      ></v-text-field>
  
      <v-data-table
        :headers="headers"
        :items="filteredProjects"
        :hide-default-footer="projects.length < 11"
        class="custom-table"
        mobile-breakpoint="600"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>
                <v-icon
              color="medium-emphasis"
              icon="mdi-book-multiple"
              size="x-small"
              start
            />              Projects
            </v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn
              class="me-2"
              prepend-icon="mdi-plus"
              rounded="lg"
              text="Add a Project"
              border
              @click="add"
            ></v-btn>
          </v-toolbar>
        </template>
  
        <template v-slot:item.status="{ value }">
          <v-chip :color="getStatusColor(value)" class="text-white">
            {{ value }}
          </v-chip>
        </template>
  
        <template v-slot:item.actions="{ item }">
          <div class="d-flex ga-2 justify-end">
            <v-icon color="medium-emphasis" icon="mdi-pencil" size="small" @click="edit(item.id)"></v-icon>
          </div>
        </template>
  
        <template v-slot:no-data>
          <v-btn
            prepend-icon="mdi-backup-restore"
            rounded="lg"
            text="Reset data"
            variant="text"
            border
            @click="reset"
          ></v-btn>
        </template>
      </v-data-table>
    </v-sheet>
  
    <v-dialog v-model="dialog" max-width="500">
      <v-card :title="isEditing ? 'Edit a Project' : 'Add a Project'">
        <v-form ref="form">
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field v-model="record.name" label="Name" :rules="nameRules" required></v-text-field>
              </v-col>
  
              <v-col cols="12" sm="6">
                <v-text-field v-model="record.location" label="Location" :rules="locationRules" required></v-text-field>
              </v-col>
  
              <v-col cols="12" sm="6">
                <v-select v-model="record.status" :items="['Pending', 'Complete', 'Canceled']" label="Status"></v-select>
              </v-col>
  
              <v-col cols="12">
                <v-textarea v-model="record.description" label="Description"></v-textarea>
              </v-col>
            </v-row>
          </v-container>
        </v-form>
  
        <v-divider></v-divider>
  
        <v-card-actions class="bg-surface-light">
          <v-btn text="Cancel" variant="plain" @click="dialog = false"></v-btn>
          <v-spacer></v-spacer>
          <v-btn text="Save" @click="save"></v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </template>
  
<script setup>
import { watch } from "vue";
import { onMounted, ref, shallowRef } from "vue";

const DEFAULT_RECORD = {
  name: "",
  location: "",
  description: "",
  status: "Pending",
};
const nameRules = [
  (v) => !!v || "Name is required",
  (v) => (v && v.length >= 3) || "Name must be at least 3 characters",
];
const locationRules = [(v) => !!v || "Location is required"];
const projects = ref([
  {
    id: 1,
    name: "Bemaal",
    location: "Riyadh",
    description: "bank system",
    status: "Pending",
  },
  {
    id: 2,
    name: "Majoz",
    location: "Jeddah",
    description: "reservtion system",
    status: "Complete",
  },
  {
    id: 3,
    name: "Moteelz",
    location: "Dubai",
    description: "booking hotels",
    status: "Canceled",
  },
]);
const record = ref(DEFAULT_RECORD);
const dialog = shallowRef(false);
const isEditing = shallowRef(false);

const headers = [
  { title: "Name", key: "name", align: "start" },
  { title: "Loction", key: "location" },
  { title: "Description", key: "description" },
  { title: "Status", key: "status", align: "start" },
  { title: "Actions", key: "actions", align: "end", sortable: false },
];

onMounted(() => {
  reset();
});

function add() {
  isEditing.value = false;
  record.value = DEFAULT_RECORD;
  dialog.value = true;
}

function edit(id) {
  isEditing.value = true;

  const found = filteredProjects.value.find((book) => book.id === id);

  record.value = {
    id: found.id,
    name: found.name,
    location: found.location,
    description: found.description,
    status: found.status,
  };

  dialog.value = true;
}

function remove(id) {
  const index = projects.value.findIndex((book) => book.id === id);
  projects.value.splice(index, 1);
}
const search = ref("");
const filteredProjects = ref([...projects.value]); // Store filtered results

watch(search, (newSearch) => {
  if (!newSearch) {
    filteredProjects.value = projects.value; // Show all items if search is empty
  } else {
    filteredProjects.value = projects.value.filter((project) =>
      project.name.toLowerCase().includes(newSearch.toLowerCase())
    );
  }
});

const form = ref(null);

const save = async () => {
  if (!form.value) return; // Ensure formRef is available

  // Reset validation to clear previous errors
  form.value.resetValidation();

  // Validate the form and check result
  let isValid;
   await form.value.validate().then((res)=>{
    isValid = res.valid 
  })
  if (isValid) {
    if (isEditing.value) {
      const index = filteredProjects.value.findIndex(
        (book) => book.id === record.value.id
      );
      filteredProjects.value[index] = record.value;
    } else {
      record.value.id = filteredProjects.value.length + 1;
      filteredProjects.value.push(record.value);
    }

    dialog.value = false;
  } else {
    return;
  }
};

function reset() {
  dialog.value = false;
  record.value = DEFAULT_RECORD;
  filteredProjects.value = [
    {
      id: 1,
      name: "Bemaal",
      location: "Riyadh",
      description: "bank system",
      status: "Pending",
    },
    {
      id: 2,
      name: "Majoz",
      location: "Jeddah",
      description: "reservtion system",
      status: "Complete",
    },
    {
      id: 3,
      name: "Moteelz",
      location: "Dubai",
      description: "booking hotels",
      status: "Canceled",
    },
  ];
}

const getStatusColor = (status) => {
  switch (status) {
    case "Pending":
      return "orange";
    case "Complete":
      return "green";
    case "Canceled":
      return "red";
    default:
      return "gray";
  }
};
</script>

<style>
.custom-table {
  height: 100%;
  max-height: 100vh;
  overflow-x: auto;

}
</style>
