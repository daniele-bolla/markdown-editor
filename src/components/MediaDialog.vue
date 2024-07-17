<template>
  <v-dialog v-model="props.modelValue" width="auto">
    <v-card max-width="600" :title="props.title">
      <v-list lines="one">
        <v-list-item>
          <v-file-input label="File input" multiple></v-file-input>
        </v-list-item>
        <v-list-item
          v-for="media in props.media"
          :key="media.id"
          :title="media.title"
          @click="selectMedia(media.id)"
          :active="isSelected(media.id)"
        >
          <a href="#">
            <v-img
              :width="400"
              aspect-ratio="16/9"
              cover
              :src="media.thumb || media.url"
            ></v-img>
          </a>
        </v-list-item>
      </v-list>
      <template v-slot:actions>
        <v-btn class="ms-auto" text="Ok" @click="onClose()"></v-btn>
        <v-btn class="ms-auto" text="Insert" @click="onInserMedia()"></v-btn>
      </template>
    </v-card>
  </v-dialog>
</template>

<script setup lang="ts">
import { ref, defineProps } from "vue";
type Media = {
  id: string;
  url: string;
  thumb?: string;
  title: string;
};

type Props = {
  title?: string;
  media?: Media[] | undefined;
  modelValue: boolean;
};
type Emit = {
  (e: "insert", selectedMedia: Media): void;
  (e: "update:modelValue", val: boolean): void;
};

const props = <Props>defineProps(["title", "media", "modelValue"]);
const emit = <Emit>defineEmits(["insert", "update:modelValue"]);

const selectedMedia = ref<Media | undefined>();

function isSelected(id: string) {
  return selectedMedia.value && selectedMedia.value.id == id;
}
function selectMedia(id: string) {
  if (props.media) {
    selectedMedia.value = props.media.find((i) => i.id == id);
  }
}
function onClose() {
  emit("update:modelValue", false);
}
function onInserMedia() {
  if (selectedMedia.value) {
    emit("insert", selectedMedia.value);
    onClose();
  }
}
</script>
