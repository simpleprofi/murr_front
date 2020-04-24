<template>
  <div>
    <label>
      <input v-model="socketMessage"
             class="m-form__control auth-input"
             type="text"
             placeholder="Введи название мурр чата">
    </label>
    <el-button
        class="ml murr-button__primary"
        @click="sendSocketMessage">
      Отправить сообщение через сокет на бекенд
    </el-button>

    <div>{{ socketMessage }}</div>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        socketMessage: '',
        socket: ''
      }
    },
    created() {
      let token = this.$store.getters.accessToken_getters
      let endpoint = 'ws://127.0.0.1:8000/ws/murr_chat/lobby/'

      this.socket = new WebSocket(endpoint + "?token=" + token)
      this.socket.onopen = function () {
        // eslint-disable-next-line no-console
        console.log('socket.onopen')
      }

      this.socket.onclose = function (event) {
        if (event.wasClean) {
          // eslint-disable-next-line no-console
          console.log('Соединение закрыто чисто')
        } else {
          // eslint-disable-next-line no-console
          console.log('Обрыв соединения')
        }
        // eslint-disable-next-line no-console
        console.log('Код: ' + event.code + ' причина: ' + event.reason)
      }

      this.socket.onmessage = function (event) {
        // eslint-disable-next-line no-console
        console.log("Получены данные " + event.data)
      }

      this.socket.onerror = function (error) {
        // eslint-disable-next-line no-console
        console.log("Ошибка " + error.message)
      }
    },
    methods: {
      sendSocketMessage() {
        this.socket.send(JSON.stringify({
          data: {
            'murr_chat_name': this.socketMessage
          },
          gan: 'create_murr_chat'
        }))
      }
    }
  }
</script>

<style scoped>

</style>