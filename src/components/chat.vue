<template>
  <b-container class="chat_container">
    <h4 class="text-right" v-if="user_name">Welcome, {{user_name}}</h4>
    <modal
        v-show="showModal"
        >
        <div class="name_input">
          <input v-model="user_name" type="text" placeholder="Your name">
        </div>
        <p style="color: rgba(255, 0, 0, 0.5)" v-if="error">{{error}}</p>
        <button @click="hideModal" class="submit_button">Submit</button>
    </modal>
    <b-row>
      <b-col class="chat_content">
        <div class="chat_messages">
          <div v-for="item in messages" :key="item.id">
            <div class="for_message" v-if="item">
              <h5>{{item.author}}</h5>
              <p>{{item.message}}</p>
            </div>
          </div>
        </div>
        <textarea name="message_area" cols="30" rows="5" v-model="message" />
        <div class="sendMessage">
          <b-button variant="success" @click="addMessage()">Send message</b-button>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import axios from 'axios'
import modal from '@/components/modal.vue'
export default {
  components: {
    modal
  },
  data () {
    return {
      serverAdress: 'http://localhost:3000/',
      showModal: true,
      user_name: '',
      message: '',
      error: '',
      messages: Array
    }
  },
  methods: {
    getMessage: function () {
      axios.get(this.serverAdress + 'posts')
        .then(response => {
          this.messages = response.data
        })
    },
    addMessage: function () {
      if (this.user_name && this.message) {
        axios.post(this.serverAdress + 'posts', {
          author: this.user_name,
          message: this.message
        })
          .then(response => {
            this.getMessage()
            this.message = ''
          })
      }
    },
    hideModal: function () {
      if (this.user_name && this.user_name.length >= 3) {
        this.showModal = false
      } else this.error = 'Name too short'
    }
  },
  mounted () {
    setInterval(() => {
      this.getMessage()
    }, 1000)
  }
}
</script>

<style lang="sass" scoped>
h1, h2, h3, h4, h5, p
  margin: 0
  padding-bottom: 0
.name_input
  width: 100%
  input
    width: 100%
.submit_button
    position: relative
    margin-top: 20px
    border: none
    padding: 6px 15px
    border-radius: 5px
    background-color: rgba(0, 200, 0, 0.6)
    font-size: 16px
    outline: none
    &:hover
      box-shadow: 2px 2px 10px 1px rgba(18, 57, 57, 0.5)
      transition: all 0.3s
.chat_container
  max-width: 650px
.chat_content
  display: flex
  padding: 20px
  flex-direction: column
  border: 1px solid rgba(0,0,0, 0.4)
  border-radius: 5px
  .chat_messages
    border: 1px solid rgba(0,0,0, 0.4)
    border-radius: 5px
    padding: 20px
    overflow-y: auto
    height: 500px
  textarea
    border-radius: 5px
    padding: 15px
    margin: 20px 0
  .sendMessage
    button
      max-width: 550px
  .for_message
    display: flex
    flex-direction: column
    text-align: left
    padding: 5px 0
    border-bottom: 1px solid rgba(0,0,0, 0.4)
    h5
      font-weight: 700
      font-size: 12px
    p
      font-size: 15px
</style>
