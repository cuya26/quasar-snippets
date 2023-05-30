<template>
  <q-page class="flex flex-center">
    <div
      @drop.prevent="this.dropFunction"
      @dragover.prevent
      @dragenter.prevent="highlight = true"
      @dragleave="highlight = false"
      :class="
        (highlight ? 'bg-info' : 'bg-primary') +
        ' row items-center justify-center text-white'
      "
      style="width: 300px; height: 300px"
    >
      Drop Zone
    </div>
    <div style="width: 50px"></div>
    <div class="column justify-center">
      <div class="row justify-center text-h6 text-teal-8">PDF Viewer</div>
      <embed
        :src="dropzoneURL"
        style="width: 400px; height: 600px"
        class="bg-teal"
        type="application/pdf"
      />
    </div>
    <div style="width: 50px"></div>
    <div>
      <div class="row justify-center text-h6 text-cyan-8">Text Area</div>
      <div
        style="
          width: 400px;
          height: 600px;
          white-space: pre-line;
          overflow: auto;
        "
        class="bg-cyan q-pa-sm"
      >
        {{ text }}
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import { api } from "boot/axios";

export default defineComponent({
  name: "drag-and-drop",
  setup() {
    return {
      dropzoneURL: ref(""),
      text: ref(""),
      highlight: ref(false),
    };
  },
  methods: {
    dropFunction(dragEvent) {
      // TODO add revokeObjectURL
      const dropzoneFile = dragEvent.dataTransfer.files[0];
      // TODO add docx
      if (dropzoneFile.type === "application/pdf") {
        this.dropzoneURL = URL.createObjectURL(dropzoneFile);
        console.log(dropzoneFile);
        console.log(dragEvent.dataTransfer);
        console.log(this.dropzoneURL);
        const uploadForm = new FormData();
        uploadForm.append("uploaded_pdf", dropzoneFile);
        // uploadForm.append("notes", "this are my notes");
        api
          .post("convert_pdf", uploadForm, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          })
          .then((response) => {
            this.text = response.data["pdf_text"];
          })
          .catch((error) => {
            console.log(error.message);
          });
      } else if (dropzoneFile.type === "text/plain") {
        const reader = new FileReader();
        reader.onload = (res) => {
          this.text = res.target.result;
        };
        reader.onerror = (err) => console.log(err);
        reader.readAsText(dropzoneFile);
      } else {
        // TODO add error message
        console.log("The dropped file haven't a supported extension");
      }
      this.highlight = false;
    },
  },
});
</script>
