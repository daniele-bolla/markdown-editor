<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto" max-width="1120px">
      <div class="text-left my-10">
        <h1 class="text-h6 text-grey_600 font-weight-bold">Content</h1>
      </div>
      <v-row>
        <v-col cols="12">
          <div class="my-0 border border-grey_300 rounded">
            <nav
              class="d-flex justify-space-between mx-4 border-grey_300 border-b"
            >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="imageDialog = true"
                >Image</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="videoDialog = true"
                >Video</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('description')"
                >Description</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('quote')"
                >Quote</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('footnote')"
                >Footnote</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('link')"
                >Link</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('button')"
                >Button</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="fileDialog = true"
                >File</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('cols')"
                >2 columns</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('table')"
                >Table</a
              >
              <a
                class="text-none text-grey_500 font-weight-regular text-decoration-none py-3"
                href="#"
                @click="insertTemplate('row')"
                >Table row</a
              >
            </nav>
            <v-textarea
              ref="editor"
              aria-placeholder="Placeholder"
              placeholder="Placeholder"
              variant="solo"
              :hide-details="true"
              v-model="textValue"
              auto-grow
            ></v-textarea>
          </div>
        </v-col>
        <v-col cols="12">
          <p class="text-grey_500 text-body-2">
            **Bold** *Italic* _Underline_ - List with bullets # H1 ## H2 ### H3
            #### H4 ##### H5 ###### H6
          </p>
        </v-col>
      </v-row>
      <v-row class="py-12">
        <v-col cols="12">
          <div v-html="result" />
        </v-col>
      </v-row>
      <MediaDialog
        @insert="onInsertMedia"
        v-model="imageDialog"
        title="Image"
        :media="media.images"
      />
      <MediaDialog
        @insert="onInsertMedia"
        v-model="videoDialog"
        title="Video"
        :media="media.videos"
      />
      <MediaDialog v-model="fileDialog" title="File" :media="media.files" />
    </v-responsive>
  </v-container>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import MarkdownIt from "markdown-it";
import MarkdownItFootnote from "markdown-it-footnote";
import MarkdownItDeflist from "markdown-it-deflist";

const md = new MarkdownIt({
  html: true,
})
  .use(MarkdownItFootnote)
  .use(MarkdownItDeflist);
const videoDialog = ref(false);
const imageDialog = ref(false);
const fileDialog = ref(false);

type Media = {
  id: string;
  url: string;
  thumb?: string;
  title: string;
};

const media = {
  videos: [
    {
      id: "vid_01",
      url: "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4",
      thumb:
        "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/images/BigBuckBunny.jpg",
      title: "Big Buck Bunny",
    },
    {
      id: "vid_02",
      url: "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ElephantsDream.mp4",
      thumb:
        "http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/images/ElephantsDream.jpg",
      title: "Elephant Dream",
    },
  ],
  images: [
    {
      id: "img_01",
      url: "https://plus.unsplash.com/premium_photo-1720971628524-f4d6e44c96a4?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      title: "Golden Hour, Kellen Riggin",
    },
    {
      id: "img_02",
      url: "https://images.unsplash.com/photo-1720831992796-dfce4a96e885?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
      title: "Golden Hour, Kellen Riggin",
    },
  ],
  files: [],
};
const editor = ref(null);
const example = `# Header 1
## Header 2

Lorem ipsum sit amet.

**bold** *italic* _underline_

## Links

[link with title](http://nodeca.github.io/pica/demo/ "title text!")

## Images

![Stormtroopocat](https://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")

## Tables

| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

Right aligned columns

| Option | Description |
| ------:| -----------:|
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

### [Footnotes](https://github.com/markdown-it/markdown-it-footnote)

Footnote 1 link[^first].

Footnote 2 link[^second].

Inline footnote^[Text of inline footnote] definition.

Duplicated footnote reference[^second].

[^first]: Footnote **can have markup**

    and multiple paragraphs.

[^second]: Footnote text.


### [Definition lists](https://github.com/markdown-it/markdown-it-deflist)

Term 1

:   Definition 1
with lazy continuation.

Term 2 with *inline markup*

:   Definition 2

        { some code, part of Definition 2 }

    Third paragraph of definition 2.

_Compact style:_

Term 1
  ~ Definition 1

Term 2
  ~ Definition 2a
  ~ Definition 2b

`;

const textValue = ref(example);
const result = computed(() => md.render(textValue.value));
const templates = {
  link: "[link text](http://example.com)",
  quote: `> Quote`,
  description: `Term 1

:   Definition 1
with lazy continuation.`,
  footnote: `Footnote 1 link[^first].
  [^first]: Footnote **can have markup**

    and multiple paragraphs.`,
  button: " <button>this is a button</button>",
  table: `| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |`,
  row: `
| cell | cell |`,
  cols: `|  |  |
| ----------- | ----------- |
| **Text**: Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum | **Text** Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum |`,
};

function insert(textToInsert: string) {
  if (editor.value) {
    const textarea: HTMLTextAreaElement = editor.value;
    let start_position = textarea.selectionStart;
    let end_position = textarea.selectionEnd;
    textValue.value = `${textarea.value.substring(
      0,
      start_position
    )}${textToInsert}${textarea.value.substring(
      end_position,
      textarea.value.length
    )}`;
  }
}
function getTemplate(template: keyof typeof templates) {
  const textToInsert = templates[template];
  return textToInsert;
}
function insertTemplate(template: keyof typeof templates) {
  const textToInsert = getTemplate(template);
  insert(textToInsert);
}
function insertTemplateMedia(title: string, url: string) {
  return `![${title}](${url} "${title}")`;
}
function onInsertMedia(selectedMedia: Media[]) {
  const textToInsert = selectedMedia.reduce((acc, media) => {
    const { title, url } = media;
    const mediaMarkdown = insertTemplateMedia(title, url);
    return (acc += `
  ${mediaMarkdown}`);
  }, "");
  insert(textToInsert);
}
</script>
