<template>
  <v-dialog v-model="props.modelValue" width="auto">
    <v-card :title="props.title">
      <v-container class="py-6 px-8">
        <v-row>
          <v-col cols="12">
            <v-file-input
              label="Upload a new media"
              @input="onUpload"
              multiple
            ></v-file-input>
          </v-col>
        </v-row>
        <v-item-group v-model="selection" multiple>
          <v-row>
            <v-col v-for="item in items" :key="item.id" cols="12" md="6">
              <v-item v-slot="{ isSelected, toggle }">
                <v-img
                  class="text-right pa-2 cursor-pointer"
                  height="220"
                  width="220"
                  aspect-ratio="16/9"
                  cover
                  :src="item.thumb || item.url"
                  @click="toggle"
                >
                  <v-btn
                    :icon="isSelected ? 'mdi-heart' : 'mdi-heart-outline'"
                  ></v-btn>
                </v-img>
              </v-item>
            </v-col>
          </v-row>
        </v-item-group>
      </v-container>
      <template v-slot:actions>
        <v-btn text="Cancel " @click="onClose()"></v-btn>
        <v-btn text="Insert" @click="onInserMedia()"></v-btn>
      </template>
    </v-card>
  </v-dialog>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
type Media = {
  id: string;
  url: string;
  thumb?: string;
  title: string;
};

type Props = {
  title?: string;
  media: Media[];
  modelValue: boolean;
};
type Emit = {
  (e: "insert", selectedMedia: Media[]): void;
  (e: "update:modelValue", val: boolean): void;
};

const props = <Props>defineProps(["title", "media", "modelValue"]);
const emit = <Emit>defineEmits(["insert", "update:modelValue"]);

const items = ref<Media[]>(props.media);
const selection = ref([]);
const selectedMedia = computed<Media[]>(() => {
  return selection.value.map((i) => items.value && items.value[i]);
});
function onClose() {
  emit("update:modelValue", false);
}
function onInserMedia() {
  if (selectedMedia.value) {
    emit("insert", selectedMedia.value);
    onClose();
  }
}
function fakeCall(data: any, time: number = 1000): Promise<Media[]> {
  let timer;
  clearTimeout(timer);
  return new Promise((resolve) => {
    timer = setTimeout(() => {
      resolve(data);
    }, time);
  });
}
const fakeResponse = [
  {
    id: "img_03",
    title: "Waterfall",
    url: "https://images.unsplash.com/photo-1721112796760-fe228d1e22a8?q=80&w=1964&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
  },
];
async function onUpload() {
  const data = await fakeCall(fakeResponse);
  items.value = items.value ? [...items.value, ...data] : data;
}
</script>
