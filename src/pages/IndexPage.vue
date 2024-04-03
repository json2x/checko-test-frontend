<style>
.app-container {
  width: 80vw;
  height: 500px;
  padding: 10px;
  border: 1px solid #ccc;
}

.story-container {
  height: 480px;
  overflow: auto;
}
</style>

<template>
  <q-page class="flex flex-center">
    <div class="app-container">
      <div class="row q-gutter-sm">
        <div class="col-4">
          <div
            class="row wrap justify-between items-start content-start q-mb-sm"
          >
            <div class="text-h5">Write a story</div>
          </div>
          <q-input
            v-model="topic"
            type="text"
            label="Topic"
            outlined
            class="q-mb-md"
          />

          <q-select
            v-model="style"
            :options="styleOptions"
            label="Style"
            outlined
            clearable
            class="q-mb-md"
          />
          <q-btn
            color="primary"
            icon="auto_fix_high"
            label="Generate Story"
            @click="generateStory"
            :loading="isGenerating"
            class="full-width"
            no-caps
          />
        </div>
        <div class="col">
          <div class="story-container">
            <q-list>
              <q-item v-for="(prop, key) in story" :key="key">
                <q-item-section>
                  <q-item-label>{{ toPropperCaseSnakeCase(key) }}</q-item-label>
                  <q-item-label caption>{{ prop }}</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </div>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from "vue";
import { useQuasar } from "quasar";
import axios from "axios";
import sampleData from "../boot/sample.json";

const $q = useQuasar();

const isGenerating = ref(false);
const topic = ref("");
const style = ref(null);
const styleOptions = ref(["Sad", "Scary", "Serious", "Adventourous"]);

const story = ref(null);

function getData() {
  console.log(sampleData);
  story.value = sampleData;
}

async function generateStory() {
  try {
    isGenerating.value = true;
    const baseUrl = import.meta.env.VITE_BACKEND_BASE_URL;
    const response = await axios.post(`${baseUrl}/v2/write_story`, {
      topic: topic.value,
      style: style.value,
    });

    if (response.status === 200) {
      story.value = response.data;
    }
  } catch (e) {
    console.error(e);
    $q.notify({
      type: "negative",
      message: "Failed to generate story",
    });
  }

  isGenerating.value = false;
}

const toPropperCaseSnakeCase = (s) => {
  const words = s.split("_");
  const properCaseString = words
    .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
    .join(" ");

  return properCaseString;
};
</script>
