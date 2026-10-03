<template>
  <div class="chat-container">
    <div class="chat-header">
      <span class="chat-status-dot" :class="{ 'status-sending': sending, 'status-error': sendError }"></span>
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
      <div v-if="sendError" class="chat-error-msg">
        تعذر إرسال الرسالة. يمكنك مراسلتنا مباشرة على <a :href="'mailto:' + contactEmail">{{ contactEmail }}</a>
      </div>
    </div>

    <!-- Step 1: Collect visitor name and email -->
    <div class="chat-form" v-if="!visitorInfo.name">
      <input v-model="nameInput" type="text" class="chat-input" placeholder="الاسم" />
      <input v-model="emailInput" type="email" class="chat-input" placeholder="البريد الإلكتروني" />
      <button class="chat-send-btn" @click="startChat" :disabled="!nameInput.trim() || !emailInput.trim()">
        ابدأ الدردشة
      </button>
    </div>

    <!-- Step 2: Chat interface -->
    <div class="chat-input-area" v-else>
      <div class="chat-visitor-info">
        <span>{{ visitorInfo.name }}</span>
        <button class="chat-change-info" @click="resetVisitorInfo">تغيير</button>
      </div>
      <input
        v-model="inputText"
        @keydown.enter="sendMessage"
        type="text"
        class="chat-input"
        :placeholder="placeholderText"
        maxlength="500"
      />
      <button class="chat-send-btn" @click="sendMessage" :disabled="!inputText.trim() || sending">
        {{ sending ? '⏳' : sendButtonText }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    contactEmail: {
      type: String,
      default: 'support@rikka.app'
    }
  },
  data() {
    return {
      inputText: '',
      nameInput: '',
      emailInput: '',
      visitorInfo: { name: '', email: '' },
      messages: [],
      typing: false,
      sending: false,
      sendError: false,
      msgId: 0,
      headerText: 'تواصل معنا',
      placeholderText: 'اكتب رسالتك هنا...',
      sendButtonText: 'إرسال',
      welcomeText: 'مرحبًا! 👋 اكتب رسالتك وسأرد عليك في أقرب وقت.',
      thankText: 'شكرًا لتواصلك معنا! تم إرسال رسالتك وسنعاود الاتصال بك قريبًا. 🌟'
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
    const savedInfo = localStorage.getItem('chatVisitorInfo')
    if (savedInfo) {
      try {
        this.visitorInfo = JSON.parse(savedInfo)
      } catch (e) {
        // ignore
      }
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
    startChat() {
      const name = this.nameInput.trim()
      const email = this.emailInput.trim()
      if (!name || !email) return
      this.visitorInfo = { name, email }
      localStorage.setItem('chatVisitorInfo', JSON.stringify(this.visitorInfo))
    },
    resetVisitorInfo() {
      this.visitorInfo = { name: '', email: '' }
      this.nameInput = ''
      this.emailInput = ''
      localStorage.removeItem('chatVisitorInfo')
    },
    async sendMessage() {
      const text = this.inputText.trim()
      if (!text) return
      this.addMessage('visitor', text)
      this.inputText = ''
      this.sending = true
      this.sendError = false
      this.typing = true
      try {
        await this.submitToEmail(text)
        this.typing = false
        this.sending = false
        this.addMessage('owner', this.thankText)
      } catch (e) {
        this.typing = false
        this.sending = false
        this.sendError = true
      }
    },
    async submitToEmail(message) {
      const response = await fetch('https://formsubmit.co/ajax/' + this.contactEmail, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify({
          name: this.visitorInfo.name,
          email: this.visitorInfo.email,
          message: message,
          _subject: 'رسالة جديدة من صفحة الدردشة - ' + this.visitorInfo.name
        })
      })
      if (!response.ok) throw new Error('Failed to send')
      return response.json()
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
  &.status-sending
    background #ffc107
  &.status-error
    background #f44336

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

.chat-error-msg
  align-self center
  font-size 0.85rem
  color #f44336
  text-align center
  padding 0.5rem
  a
    color $accentColor
    text-decoration underline

.chat-form
  display flex
  flex-wrap wrap
  padding 0.8rem
  border-top 1px solid #eaecef
  background #fff
  gap 0.5rem

.chat-input-area
  display flex
  flex-wrap wrap
  padding 0.8rem
  border-top 1px solid #eaecef
  background #fff
  gap 0.5rem

.chat-visitor-info
  width 100%
  display flex
  align-items center
  justify-content space-between
  font-size 0.8rem
  color #666
  margin-bottom 0.3rem

.chat-change-info
  background none
  border none
  color $accentColor
  cursor pointer
  font-size 0.8rem
  text-decoration underline

.chat-input
  flex 1
  min-width 150px
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
