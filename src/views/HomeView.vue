<script>
import AppHeader from '@/components/AppHeader.vue'
import WelcomeView from '@/components/WelcomeView.vue'
import DiscussionView from '@/components/DiscussionView.vue'
import OpenAI from 'openai'

const openai = new OpenAI({
  apiKey: import.meta.env.VITE_OPEN_API_KEY,
  dangerouslyAllowBrowser: true
})

export default {
  components: { AppHeader, WelcomeView, DiscussionView },
  data() {
    return {
      messageToSend: '',
      apiResponse: null,
      answerLoading: false,
      discussion: []
    }
  },
  methods: {
    async chatCompletion() {
      event.preventDefault()
      if (this.messageToSend != '') {
        const messageToSend = this.messageToSend
        this.messageToSend = ''
        this.discussion.push({ text: messageToSend, isUser: true })
        this.answerLoading = true

        await openai.chat.completions
          .create({
            messages: [{ role: 'user', content: messageToSend }],
            model: 'gpt-3.5-turbo'
          })
          .then((result) => {
            this.apiResponse = result?.choices[0]?.message?.content
          })
          .catch((err) => {
            console.log(err)
          })

        this.discussion.push({ text: this.apiResponse, isUser: false })
        this.answerLoading = false
      }
    }
  }
}
</script>

<template>
  <!-- Header -->
  <div
    class="fixed w-full md:w-[80%] top-0 md:right-0 md:left-[20%] p-4 flex items-center justify-between bg-inherit"
  >
    <AppHeader />
  </div>

  <!-- Content -->
  <div class="my-20 mb-24 w-full flex items-center justify-center">
    <div v-if="discussion.length !== 0" class="flex items-center justify-center w-4/5 md:w-3/5">
      <div class="w-full">
        <DiscussionView
          v-for="(message, index) in discussion"
          :key="index"
          :text="message.text"
          class="w-full divide-y divide-solid text-justify"
          :class="message.isUser === true ? 'bg-gray-200 p-2 mt-1' : 'bg-slate-50 p-4'"
        />
      </div>
    </div>
    <WelcomeView v-else />
  </div>

  <!-- Form -->
  <div class="fixed w-full md:w-[80%] bottom-0 md:right-0 md:left-[20%] pt-4 pb-6 bg-inherit">
    <form class="flex items-center justify-center">
      <div class="flex items-stretch justify-center w-4/5 md:w-3/5 relative">
        <textarea
          name=""
          id=""
          rows="1"
          class="w-full resize-none border border-[#9b9b9b] bg-transparent overflow-none rounded-lg focus:outline-none ring-0 shadow-sm shadow-black text-white p-4 caret-white"
          placeholder="Message ChatGPT..."
          v-model="messageToSend"
          @keyup.enter="chatCompletion"
        ></textarea>
        <button
          class="text-[#9b9b9b] absolute right-2 inset-y-0"
          title="Send message"
          @click="chatCompletion"
        >
          <span class="material-symbols-outlined">send</span>
        </button>
      </div>
    </form>
  </div>
</template>
