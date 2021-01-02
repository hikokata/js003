<template>
  <div id="app">
    <main class="main-container">
      <div class="chat-timeline">
        <balloon
          v-for="chat in chatLogs"
          :speaker="chat.speaker"
          :name="chat.name"
          :key="chat.message"
          :message="chat.message"
        >
        </balloon>
      </div>
      <chat-form @submit-message="submit" apply-event="submit-message">
      </chat-form>
    </main>
  </div>
</template>

<script>
import Balloon from "./components/Balloon.vue";
import ChatForm from "./components/ChatForm.vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    balloon: Balloon,
    chatForm: ChatForm
  },
  data() {
    return {
      chatLogs: [
        {
          name: "bot",
          speaker: "other",
          message:
            "入力した言葉を広辞苑などの辞書で調べるLINE風 Bot です。よろしく～"
        },
        { name: "私", speaker: "my", message: "こんにちわぁ" },
        { name: "bot", speaker: "other", message: "はい、こんにちは。" }
      ]
    };
  },
  methods: {
    submit(value) {
      this.chatLogs.push({
        name: "私",
        speaker: "my",
        message: value
      });
      this.botSubmit(value);
      this.scrollDown();
    },
    botSubmit(value) {
      let _this = this;

      axios({
        method: "GET",
        url:
          "https://sakura-paris.org" +
          "/dict/?api=1" +
          "&dict=広辞苑_日本大百科_マグローヒル科学技術用語大辞典&type=0&q=" +
          value,
        //origin: "https://hikokata.github.io",
        timeout: 1000 // ms
      })
        /*
      fetch(
        "https://sakura-paris.org" +
          "/dict/?api=1" +
          "&dict=広辞苑_日本大百科_マグローヒル科学技術用語大辞典&type=0&q=" +
          value,
        {
          mode: "cors"
        }
      )
      */
        /*
      axios({
        method: "POST",
        url:
          //  "https://ja.wikipedia.org/w/api.php?format=json&action=query&list=search&srlimit=200&srnamespace=0&utf8=true&srsearch=" +
          //  value +
          "https://en.wikipedia.org/w/api.php?format=json&action=query&generator=search&gsrnamespace=0&gsrlimit=10&prop=pageimages|extracts&pilimit=max&exintro&explaintext&exsentences=1&exlimit=max&gsrsearch=" +
          value +
          "&callback=JSON_CALLBACK",
        origin: "https://www.mediawiki.org"
      })
      */
        .then(function(res) {
          let text = "意味が分かりません。";
          if (res.data.length > 0) {
            text = res.data[0][0].text;
          }
          _this.chatLogs.push({
            name: "bot",
            speaker: "other",
            message: text
          });
          _this.scrollDown();
        })
        .catch(function(err) {
          _this.chatLogs.push({
            name: "bot",
            speaker: "other",
            message: "辞書サーバーと通信できませんでした。" + err
          });
          _this.scrollDown();
        });
      /*
      setTimeout(() => {
        this.chatLogs.push({
          name: "bot",
          speaker: "other",
          message: "はい、はい。そうですね。"
        });

        this.scrollDown();
      }, 1000);
      */
    },
    scrollDown() {
      const target = this.$el.querySelector(".chat-timeline");
      setTimeout(() => {
        const height = target.scrollHeight - target.offsetHeight;
        target.scrollTop += 10;

        if (height <= target.scrollTop) {
          return;
        } else {
          this.scrollDown();
        }
      }, 0);
    }
  }
};
</script>

<style>
/* base */
body {
  font-size: 62.5%;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

button {
  background-color: transparent;
  border: none;
  cursor: pointer;
  outline: none;
  padding: 0;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}

/* timeline */
.chat-timeline {
  position: fixed;
  top: 0;
  bottom: 40px;
  left: 0;
  right: 0;
  min-width: 480px;
  padding: 30px;
  background-color: #88a4d4;
  overflow-y: auto;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
}

.chat-timeline > .conversation-balloon {
  margin-bottom: 30px;
}

.conversation-balloon.my {
  text-align: right;
}

.conversation-balloon.my > .avatar {
  float: right;
}

.conversation-balloon.my > .message {
  margin-right: 20px;
  background-color: #85ff49;
  text-align: left;
}

.conversation-balloon.my > .message::before {
  right: -20px;
  -webkit-transform: rotate(-25deg);
  transform: rotate(-25deg);
  border-left: 18px solid #85ff49;
}

.conversation-balloon.other {
  text-align: left;
}

.conversation-balloon.other > .avatar {
  float: left;
}

.conversation-balloon.other > .message {
  margin-left: 20px;
  background-color: #ffffff;
}

.conversation-balloon.other > .message::before {
  left: -20px;
  -webkit-transform: rotate(25deg);
  transform: rotate(25deg);
  border-right: 18px solid #ffffff;
}

.conversation-balloon > .avatar {
  width: 80px;
}

.conversation-balloon > .avatar::after {
  clear: both;
}

.conversation-balloon > .avatar > img {
  display: block;
  margin: 0 auto;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  margin-bottom: 5px;
}

.conversation-balloon > .avatar > .name {
  width: 100%;
  text-align: center;
  color: white;
  font-size: 0.8rem;
  word-wrap: break-word;
}

.conversation-balloon > .message {
  display: inline-block;
  max-width: 280px;
  padding: 10px 30px;
  border-radius: 30px;
  font-size: 1.3rem;
  min-height: 30px;
  word-wrap: break-word;
  position: relative;
}

.conversation-balloon > .message::before {
  content: "";
  display: block;
  position: absolute;
  top: 5px;
  border: 8px solid transparent;
}

/* form */
.chat-form {
  position: fixed;
  bottom: 0;
  right: 0;
  left: 0;
  background-color: white;
}

.form-container {
  position: relative;
  height: 40px;
}

.form-container > .message {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 80px;
  width: 100%;
  font-size: 1.3rem;
  padding: 0 20px;
}

.form-container > .submit {
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  width: 80px;
  text-align: center;
  background-color: #4f83e1;
  color: white;
  font-size: 1.6rem;
}
/*# sourceMappingURL=style.css.map */
</style>
