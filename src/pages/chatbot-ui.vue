<template>
  <q-page padding class="row items-center jusitfy-center">
    <div
      class="column justify-center items-center no-wrap col-12"
      style="height: 85vh"
    >
      <div
        style="
          height: 100%;
          width: 70%;
          border-radius: 4px;
          border: 1.5px solid #bdc3c7;
        "
        class="overflow-auto q-pa-md"
        ref="chatWindow"
      >
        <div class="q-px-sm row justify-center" style="height: 100%">
          <div class="col-12">
            <div
              v-for="chatLine in chatStory"
              :key="chatLine"
              :class="
                'row justify-' +
                chatConfig['chatLinePosition'][chatLine.sender] +
                ' q-py-sm'
              "
            >
              <div
                :class="
                  'bg-' +
                  chatConfig['chatLineColor'][chatLine.sender] +
                  ' q-pa-sm'
                "
                style="border-radius: 12px; width: fit-content; max-width: 60%"
              >
                {{ chatLine.text }}
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="q-pa-sm"></div>
      <div class="row justify-center no-wrap" style="width: 70%">
        <q-input
          style="width: 100%"
          rounded
          outlined
          dense
          v-model="inputText"
          placeholder="Write a message"
          @keyup.enter="sendMessage(inputText)"
        />
        <div class="q-px-sm"></div>
        <q-btn
          round
          color="primary"
          icon="send"
          @click="sendMessage(inputText)"
        />
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import { api } from "boot/axios";

const chatConfig = {
  chatLineColor: {
    assistant: "purple-5",
    user: "teal-5",
  },
  chatLinePosition: {
    assistant: "begin",
    user: "end",
  },
};
export default {
  // name: 'PageName',
  setup() {
    return {
      inputText: ref(""),
      chatConfig,
      chatStory: ref([
        { text: "Hi, I'm Vicuna how can I help you?", sender: "assistant" },
        { text: "Ciao Vicuna, parli anche italiano?", sender: "user" },
      ]),
    };
  },
  methods: {
    sendMessage(myText) {
      this.$refs.chatWindow.scrollTop = this.$refs.chatWindow.scrollHeight;
      if (myText === "") return;
      this.chatStory.push({ text: myText, sender: "user" });
      this.inputText = "";
      api
        .post("/send_message", { message: myText })
        .then((response) => {
          this.chatStory.push({
            text: response.data.answer,
            sender: "assistant",
          });
          this.$nextTick(() => {
            this.$refs.chatWindow.scrollTop =
              this.$refs.chatWindow.scrollHeight;
          });
        })
        .catch((error) => {
          error.message;
          this.chatStory.push({
            text: "There was a connection error, sorry",
            sender: "assistant",
          });
          this.$nextTick(() => {
            this.$refs.chatWindow.scrollTop =
              this.$refs.chatWindow.scrollHeight;
          });
        });
    },
  },
};
</script>
