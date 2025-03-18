<template>
  <v-sheet border rounded>
    <div>
      <v-text-field
        v-model="search"
        label="Search By Name"
        class="mt-5 mx-3"
      ></v-text-field>
    </div>
    <v-data-table
      :headers="headers"
      :hide-default-footer="spaces.length < 11"
      :items="filteredSpaces"
      class="custom-table"
    >
      <template #top>
        <v-toolbar flat>
          <v-toolbar-title>
            <v-icon
              color="medium-emphasis"
              icon="mdi-book-multiple"
              size="x-small"
              start
            />

            Projects
          </v-toolbar-title>

          <v-spacer />

          <v-btn
            class="me-2"
            prepend-icon="mdi-plus"
            rounded="lg"
            text="Add a Space"
            border
            @click="add"
          />
        </v-toolbar>
      </template>

      <template #item.title="{ value }">
        <v-chip
          :text="value"
          border="thin opacity-25"
          prepend-icon="mdi-book"
          label
        >
          <template #prepend>
            <v-icon color="medium-emphasis" />
          </template>
        </v-chip>
      </template>

      <template #item.actions="{ item }">
        <div class="d-flex ga-2 justify-end">
          <v-icon
            color="medium-emphasis"
            icon="mdi-pencil"
            size="small"
            @click="edit(item.id)"
          />

          <v-icon
            color="medium-emphasis"
            icon="mdi-delete"
            size="small"
            @click="remove(item.id)"
          />
        </div>
      </template>

      <template #no-data>
        <v-btn
          prepend-icon="mdi-backup-restore"
          rounded="lg"
          text="Reset data"
          variant="text"
          border
          @click="reset"
        />
      </template>
    </v-data-table>
  </v-sheet>

  <v-dialog v-model="dialog" max-width="500">
    <v-card
      :subtitle="`${isEditing ? 'Update' : 'Create'} your favorite Space`"
      :title="`${isEditing ? 'Edit' : 'Add'} a Space`"
    >
      <template #text>
        <v-form ref="form">
          <v-row>
            <v-col cols="12">
              <v-text-field
                v-model="record.name"
                label="Name"
                :rules="nameRules"
                required
              />
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="record.area"
                label="Area"
                :rules="areaRules"
                required
              />
            </v-col>

            <v-col cols="12" md="6">
              <v-select
                v-model="record.level"
                :items="['First Level', 'Second Level', 'Third Level']"
                label="Level"
              />
            </v-col>
          </v-row>
        </v-form>
      </template>

      <v-divider />

      <v-card-actions class="bg-surface-light">
        <v-btn text="Cancel" variant="plain" @click="dialog = false" />

        <v-spacer />

        <v-btn text="Save" @click="save" />
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script setup>
import { onMounted, ref, shallowRef } from "vue";
const DEFAULT_RECORD = {
  name: "",
  area: "",
  level: "First Level",
};

const areaRules = [(v) => !!v || "Area is required"];
const nameRules = [
  (v) => !!v || "Name is required",
  (v) => (v && v.length >= 3) || "Name must be at least 3 characters",
];
const spaces = ref([
  {
    id: 1,
    name: "Space One",
    area: "Tanta",
    level: "First Level",
  },
  {
    id: 2,
    name: "Space Two",
    area: "Cairo",
    level: "Second Level",
  },
  {
    id: 3,
    name: "Space There",
    area: "Mansoura",
    level: "Third Level",
  },
]);
const record = ref(DEFAULT_RECORD);
const dialog = shallowRef(false);
const isEditing = shallowRef(false);

const headers = [
  { title: "Name", key: "name", align: "start" },
  { title: "Area", key: "area" },
  { title: "Level", key: "level" },
  { title: "Actions", key: "actions", align: "end", sortable: false },
];

onMounted(() => {
  reset();
});

function add() {
  isEditing.value = false;
  record.value = {
    name: "",
    area: "",
    level: "First Level",
  };
  console.log(record.value);

  dialog.value = true;
}
const search = ref("");
const filteredSpaces = ref([...spaces.value]); // Store filtered results

watch(search, (newSearch) => {
  if (!newSearch) {
    filteredSpaces.value = spaces.value; // Show all items if search is empty
  } else {
    filteredSpaces.value = spaces.value.filter((space) =>
      space.name.toLowerCase().includes(newSearch.toLowerCase())
    );
  }
});
function edit(id) {
  isEditing.value = true;

  const found = filteredSpaces.value.find((book) => book.id === id);

  record.value = {
    id: found.id,
    name: found.name,
    area: found.area,
    level: found.level,
  };

  dialog.value = true;
}

function remove(id) {
  const index = filteredSpaces.value.findIndex((book) => book.id === id);
  filteredSpaces.value.splice(index, 1);
}
const form = ref(null);

const save = async () => {
  if (!form.value) return; // Ensure formRef is available

  // Reset validation to clear previous errors
  form.value.resetValidation();

  // Validate the form and check result
  let isValid;
  await form.value.validate().then((res) => {
    isValid = res.valid;
  });
  if (isValid) {
    if (isEditing.value) {
      const index = filteredSpaces.value.findIndex(
        (book) => book.id === record.value.id
      );
      filteredSpaces.value[index] = record.value;
    } else {
      record.value.id = filteredSpaces.value.length + 1;
      filteredSpaces.value.push(record.value);
    }

    dialog.value = false;
  } else {
    return;
  }
};

function reset() {
  dialog.value = false;
  record.value = DEFAULT_RECORD;
  filteredSpaces.value = [
    {
      id: 1,
      name: "Space One",
      area: "Tanta",
      level: "First Level",
    },
    {
      id: 2,
      name: "Space Two",
      area: "Cairo",
      level: "Second Level",
    },
    {
      id: 3,
      name: "Space There",
      area: "Mansoura",
      level: "Third Level",
    },
  ];
}
</script>

<style>
.custom-table {
  height: 100%;
  max-height: 100vh;
}
</style>
