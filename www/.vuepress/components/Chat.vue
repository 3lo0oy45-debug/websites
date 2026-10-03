<template>
  <div class="chat-container">
    <div class="chat-header">
      <span class="chat-status-dot"></span>
      <span class="chat-header-text">{{ headerText }}</span>
    </div>
    <div class="chat-messages" ref="messagesContainer">
      <div
        v-for="msg in messages"
        :key="msg.id"
        :class="['chat-message', msg.from === 'visitor' ? 'chat-message-visitor' : 'chat-message-owner']"
      >
        <span class="chat-message-text">{{ msg.text }}</span>
        <span class="chat-message-time">{{ msg.time }}</span>
      </div>
      <div v-if="typing" class="chat-typing">
        <span></span><span></span><span></span>
      </div>
    </div>
    <div class="chat-input-area">
      <input
        v-model="inputText"
        @keydown.enter="sendMessage"
        type="text"
        class="chat-input"
        :placeholder="placeholderText"
        maxlength="500"
      />
      <button class="chat-send-btn" @click="sendMessage" :disabled="!inputText.trim()">
        {{ sendButtonText }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputText: '',
      messages: [],
      typing: false,
      msgId: 0,
      headerText: 'تواصل معنا',
      placeholderText: 'اكتب رسالتك هنا...',
      sendButtonText: 'إرسال',
      welcomeText: 'مرحبًا! 👋 اكتب رسالتك وسأرد عليك في أقرب وقت.',
      thankText: 'شكرًا لتواصلك معنا! تم استلام رسالتك وسنعاود الاتصال بك قريبًا. 🌟'
    }
  },
  mounted() {
    const saved = localStorage.getItem('chatMessages')
    if (saved) {
      try {
        this.messages = JSON.parse(saved)
        this.msgId = this.messages.length ? Math.max(...this.messages.map(m => m.id)) + 1 : 0
      } catch (e) {
        this.messages = []
      }
    }
    if (!this.messages.length) {
      this.addMessage('owner', this.welcomeText)
    }
  },
  methods: {
    formatTime() {
      const now = new Date()
      return now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0')
    },
    addMessage(from, text) {
      this.messages.push({
        id: this.msgId++,
        from,
        text,
        time: this.formatTime()
      })
      this.saveMessages()
      this.$nextTick(this.scrollToBottom)
    },
    sendMessage() {
      const text = this.inputText.trim()
      if (!text) return
      this.addMessage('visitor', text)
      this.inputText = ''
      this.typing = true
      setTimeout(() => {
        this.typing = false
        this.addMessage('owner', this.thankText)
      }, 1500)
    },
    saveMessages() {
      localStorage.setItem('chatMessages', JSON.stringify(this.messages))
    },
    scrollToBottom() {
      const el = this.$refs.messagesContainer
      if (el) el.scrollTop = el.scrollHeight
    }
  }
}
</script>

<style lang="stylus" scoped>
.chat-container
  max-width 600px
  margin 2rem auto
  border 1px solid #eaecef
  border-radius 12px
  overflow hidden
  background #fff
  display flex
  flex-direction column
  height 500px

.chat-header
  background $accentColor
  color #fff
  padding 0.8rem 1rem
  display flex
  align-items center
  gap 0.5rem

.chat-status-dot
  width 10px
  height 10px
  border-radius 50%
  background #4caf50
  display inline-block

.chat-header-text
  font-size 1.1rem
  font-weight 500

.chat-messages
  flex 1
  overflow-y auto
  padding 1rem
  display flex
  flex-direction column
  gap 0.6rem
  background #f5f5f5

.chat-message
  max-width 75%
  padding 0.6rem 1rem
  border-radius 12px
  display flex
  flex-direction column
  gap 0.2rem

.chat-message-visitor
  align-self flex-end
  background $accentColor
  color #fff
  border-bottom-right-radius 4px

.chat-message-owner
  align-self flex-start
  background #fff
  color $textColor
  border 1px solid #eaecef
  border-bottom-left-radius 4px

.chat-message-text
  font-size 0.95rem
  line-height 1.4
  word-break break-word

.chat-message-time
  font-size 0.7rem
  opacity 0.6

.chat-typing
  display flex
  gap 4px
  padding 0.6rem 1rem

.chat-typing span
  width 8px
  height 8px
  border-radius 50%
  background #999
  animation typing 1.2s infinite

.chat-typing span:nth-child(2)
  animation-delay 0.2s

.chat-typing span:nth-child(3)
  animation-delay 0.4s

@keyframes typing
  0%, 60%, 100%
    opacity 0.3
  30%
    opacity 1

.chat-input-area
  display flex
  padding 0.8rem
  border-top 1px solid #eaecef
  background #fff

.chat-input
  flex 1
  border 1px solid #eaecef
  border-radius 8px
  padding 0.6rem 0.8rem
  font-size 0.95rem
  outline none
  &:focus
    border-color $accentColor

.chat-send-btn
  background $accentColor
  color #fff
  border none
  border-radius 8px
  padding 0.6rem 1.2rem
  margin-left 0.5rem
  cursor pointer
  font-size 0.95rem
  transition background 0.2s
  &:disabled
    opacity 0.5
    cursor not-allowed
  &:not(:disabled):hover
    background lighten($accentColor, 10%)

@media (max-width: 719px)
  .chat-container
    height 400px
    margin 1rem 0.5rem
</style>
