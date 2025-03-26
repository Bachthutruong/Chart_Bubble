<template>
  <div class="chat-bubble">
    <div class="bubble-icon" @click="toggleChat">
      <i class="fas fa-comments"></i>
    </div>

    <div v-if="isChatOpen" class="chat-window">
      <div class="chat-header">
        <span>Chat với Bot</span>
        <button class="close-btn" @click="toggleChat">
          <i class="fas fa-times"></i>
        </button>
      </div>
      <div class="chat-body" ref="chatBody">
        <div class="messages-container">
          <div
            v-for="(message, index) in messages"
            :key="index"
            class="message-card"
            :class="
              message && message.type === 'user'
                ? 'user-message'
                : 'bot-message'
            "
          >
            <div class="card" v-if="message">
              <div class="card-body">
                <div class="card-body-user" v-if="message.type === 'user'">
                  {{ message.text }}
                </div>
                <div
                  class="card-body-bot"
                  v-else
                  v-html="renderMarkdown(message.text)"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="chat-footer">
        <textarea
          v-model="inputMessage"
          @keydown.enter.exact="handleEnterKey"
          @keydown.shift.enter.stop
          @input="adjustTextareaHeight"
          placeholder="Nhập tin nhắn..."
          ref="textarea"
        ></textarea>
        <button @click="sendMessage" class="send-btn">
          <i class="fas fa-paper-plane"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { marked } from "marked";

export default {
  data() {
    return {
      isChatOpen: false,
      inputMessage: "",
      messages: [],
      isMobileDevice: false,
    };
  },
  mounted() {
    this.isMobileDevice = window.innerWidth <= 768;
    window.addEventListener("resize", this.checkDevice);
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.checkDevice);
  },
  methods: {
    checkDevice() {
      this.isMobileDevice = window.innerWidth <= 768;
    },
    toggleChat() {
      this.isChatOpen = !this.isChatOpen;
      if (this.isChatOpen) {
        this.messages = [...this.messages].reverse();
        this.$nextTick(() => {
          this.scrollToBottom();
        });
      }
    },
    sendMessage() {
      if (this.inputMessage.trim() !== "") {
        this.messages.push({ type: "user", text: this.inputMessage });
        const botReply = `**Bot:** Đây là câu trả lời Markdown từ bot.`;
        this.messages.push({ type: "bot", text: botReply });
        this.inputMessage = "";
        this.$nextTick(() => {
          this.scrollToBottom();
          this.resetTextareaHeight();
        });
      }
    },
    handleEnterKey(event) {
      if (this.isMobileDevice) {
        return;
      } else {
        event.preventDefault();
        this.sendMessage();
      }
    },
    scrollToBottom() {
      const chatBody = this.$refs.chatBody;
      chatBody.scrollTop = chatBody.scrollHeight;
    },
    renderMarkdown(text) {
      return marked(text);
    },
    adjustTextareaHeight() {
      const textarea = this.$refs.textarea;
      textarea.style.height = "36px";
      const scrollHeight = textarea.scrollHeight;
      const maxHeight = 150;
      textarea.style.height = `${Math.min(scrollHeight, maxHeight)}px`;
    },
    resetTextareaHeight() {
      const textarea = this.$refs.textarea;
      textarea.style.height = "36px";
    },
  },
};
</script>

<style scoped>
.chat-bubble {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 1000;
}

.close-btn {
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

.bubble-icon {
  width: 50px;
  height: 50px;
  background-color: #129ddb;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.chat-window {
  position: fixed;
  bottom: 80px;
  right: 20px;
  width: 400px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.chat-header {
  background: #129ddb;
  color: white;
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.chat-body {
  height: 400px;
  padding: 10px;
  background: #f9f9f9;
  background-image: url("@/assets/image_bot.png");
  background-size: 50%;
  background-position: center;
  background-repeat: no-repeat;
  overflow-y: auto;
}

.messages-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.message-card {
  display: flex;
}

.user-message {
  text-align: left;
  color: #007bff;
  justify-content: flex-end;
}

.bot-message {
  text-align: left;
  color: #333;
  justify-content: flex-start;
}

.card {
  max-width: 80%;
  border: 1px solid #ddd;
  border-radius: 10px;
  background: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  white-space: pre-wrap;
  word-wrap: break-word;
}

.card-body {
  font-size: 14px;
  word-wrap: break-word;
  white-space: pre-wrap;
}

.card-body-user {
  font-size: 14px;
  word-wrap: break-word;
  color: white;
  white-space: pre-wrap;
}

.card-body-bot {
  font-size: 14px;
  word-wrap: break-word;
  white-space: pre-wrap;
}

.user-message .card {
  background-color: #129ddb;
}

.bot-message .card {
  background-color: #ffffff;
}

.chat-footer {
  display: flex;
  align-items: flex-end;
  padding: 10px;
  background: #f9f9f9;
  border-top: 1px solid #ddd;
}

.chat-footer textarea {
  flex: 1;
  resize: none;
  padding: 5px;
  border: 1px solid #ddd;
  border-radius: 5px;
  overflow: hidden;
  min-height: 36px;
  height: 36px;
  box-sizing: border-box;
}

.chat-footer button {
  margin-left: 10px;
  background: #129ddb;
  color: white;
  border: none;
  height: 36px;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}

@media (max-width: 768px) {
  .chat-window {
    bottom: 0;
    right: 0;
    width: 100%;
    height: 75vh;
    border-radius: 0;
  }

  .chat-header {
    font-size: 16px;
    padding: 15px;
  }

  .chat-body {
    height: calc(75vh - 130px);
    padding: 15px;
  }

  .chat-footer {
    position: sticky;
    bottom: 0;
    background: #f9f9f9;
    z-index: 10;
  }
}
</style>
