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
    <div class="progress" v-if="loading">
      <div class="progress-bar progress-bar-striped progress-bar-animated" role="progressbar" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100" style="width: 75%"></div>
    </div>
    
    <ul v-else class="list-group">
      <li v-for="tweet in tweets" :key="tweet._id" class="list-group-item">
        {{ tweet.content }} - {{ tweet.location }} - {{ tweet.author.firstname }} {{ tweet.author.lastname }} - {{ tweet.author.createdAt }}
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
        withCredentials: true
      })
      .then( response => {
        this.loading = false;
        this.tweets = response.data.data;
      })
    },
    _loadAuthors(){
      axios.get(`${config.baseURL}/authors`,{
        withCredentials: true
      })
      .then( response => {
        this.authors = response.data.users;
      })
    },
    create(){
      
      let payload = "";
      Object.keys(this.tweet).forEach(key => {
        payload+=`${key}=${this.tweet[key]}&`
      });
      
      axios.post(`${config.baseURL}/tweets`, payload, {
        withCredentials: true,
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
