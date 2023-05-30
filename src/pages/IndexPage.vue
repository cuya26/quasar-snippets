<template>
  <q-page class="flex flex-center">
    <div
      @drop.prevent="this.dropFunction"
      @dragover.prevent
      @dragenter.prevent
      class="bg-primary row items-center justify-center text-white"
      style="width: 300px; height: 300px"
    >
      Drop Zone
    </div>
    <div style="width: 50px"></div>
    <embed
      :src="dropzoneFile"
      style="width: 400px; height: 650px"
      class="bg-teal"
      type="application/pdf"
    />
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "IndexPage",
  setup() {
    dropzoneFile: ref("");
  },
  methods: {
    dropFunction(dragEvent) {
      this.dropzoneFile = URL.createObjectURL(dragEvent.dataTransfer.files[0]);
      console.log(dragEvent.dataTransfer);
      console.log(this.dropzoneFile);
    },
  },
});
</script>
