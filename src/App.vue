<template>
  <div id="app" class="container">
    <form id="form">
      <div class="form-group">
        <label>Content:</label>
        <textarea name="content" v-model="tweet.content" class="form-control" rows="3"></textarea>
      </div>
      <div class="form-group">
        <label>Location:</label>
        <input type="text" class="form-control" name="location" placeholder="Location" v-model="tweet.location">
      </div>
      <div class="form-group">
        <label>Author:</label>
        <select name="author" v-model="tweet.author"  class="form-control">
          <option v-for="author in authors" :value="author._id">
            {{ author.firstname }} {{ author.lastname }}
          </option>
        </select>
      </div>
      <button @click.prevent="create" class="btn btn-primary">Create</button>
    </form>
    <hr />
    <p v-if="loading">Loading ...</p>
    <ul v-else>
      <li v-for="tweet in tweets" :key="tweet._id">
        {{ tweet.content }} - {{ tweet.author.firstname }} {{ tweet.author.lastname }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
import config from "./config.json";

export default {
  name: 'app',
  data () {
    return {
      loading: true,
      tweets: [],
      authors: [],
      tweet: {
        content: '',
        location: '',
        author: ''
      }
    }
  },
  created (){
    this._loadTweets();
    this._loadAuthors();
  },
  methods: {
    _loadTweets(){
      axios.get(`${config.baseURL}/tweets`,{
        withCredentials: false
      })
      .then( response => {
        this.loading = false;
        this.tweets = response.data.data;
      })
    },
    _loadAuthors(){
      axios.get(`${config.baseURL}/authors`,{
        withCredentials: false
      })
      .then( response => {
        this.authors = response.data.data;
      })
    },
    create(){
      
      let payload = "";
      Object.keys(this.tweet).forEach(key => {
        payload+=`${key}=${this.tweet[key]}&`
      });
      
      axios.post(`${config.baseURL}/tweets`, payload, {
        withCredentials: false,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      })
      .then( response => {
        this._loadTweets();
      })
    }
  }
}
</script>