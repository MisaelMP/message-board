<template>
<div>
  <button @click="showMessageForm = !showMessageForm" type="button" class="btn btn-secondary btn-sm mt-3 mb-3">Toggle Message Form</button>
  <form v-if="showMessageForm" @submit.prevent="addMessage" class="mb-3">
    <div v-if="error" class="alert alert-warning alert-dismissible fade show" role="alert">
      <strong>Holy guacamole!</strong> {{error}}
      <button type="button" class="close" data-dismiss="alert" aria-label="Close">
        <span aria-hidden="true">&times;</span>
      </button>
    </div>
    <div class="form-group">
      <label for="username">Username</label>
      <input v-model="message.username" type="text" class="form-control" id="username" required>
    </div>
    <div class="form-group">
      <label for="subject">Subject</label>
      <input v-model="message.subject" type="text" class="form-control" id="subject" placeholder="Enter a subject" required>
    </div>
    <div class="form-group">
      <label for="message">Message</label>
      <textarea v-model="message.message" class="form-control" id="message" rows="3"></textarea>
    </div>
    <div class="form-group">
      <label for="imageURL">Image URL</label>
      <input v-model="message.imageURL" type="url" class="form-control" id="imageURL" placeholder="Place and image url">
    </div>
    <button type="submit" class="btn btn-secondary btn-lg">Add message</button>
  </form>
  <div class="list-unstyled" v-for="message in reversedMessages" :key="message._id">
    <li class="media">
      <img v-if="message.imageURL" class="mr-3" :src="message.imageURL" :alt="message.subject">
      <div class="media-body">
        <h4 class="mt-0 mb-1">{{message.username}}</h4>
        <h5 class="mt-0 mb-1">{{message.subject}}</h5>
        {{message.message}}
        <br />
        <small>{{message.created}}</small>
      </div>
    </li>
    <hr />
  </div>
</div>
</template>

<script>
const API_URL = 'http://localhost:1234/messages';

export default {
  name: 'home',
  data: () => ({
    showMessageForm: false,
    error: '',
    messages: [],
    message: {
      username: 'Anonymous',
      subject: '',
      message: '',
      imageURL: '',
    },
  }),
  computed: {
    reversedMessages() {
      return this.messages.slice().reverse();
    }
  },
  mounted() {
    fetch(API_URL).then(response => response.json()).then((result) => {
      this.messages = result;
    });
  },
  methods: {
    addMessage() {
      console.log(this.message);
      fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(this.message),
        headers: {
          'content-type': 'application/json'
        }
      }).then(response => response.json()).then((result) => {
        if (result.details) {
          // there was an error
          const error = result.details.map(detail => detail.message).join('. ');
          this.error = error;
        } else {
          this.error = '';
          this.showMessageForm = false;
          this.messages.push(result);
        }
      });
    }
  },
};
</script>

<style>
hr {
  border: 1px solid #f8f8ff;
}

img {
  max-width: 30vw;
  height: 30vh;
  border-radius: .5em;
}
</style>
