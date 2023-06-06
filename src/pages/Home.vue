<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">
        <div class="error" v-if="error">
          <p>{{ error }}</p>
        </div>

        <search
          :value="search"
          placeholder="Type user name"
          @search="search = $event"
        />

        <button v-if="!repos" class="btn btnPrimary" @click="getRepos">Search</button>
        <button v-else class="btn btnPrimary" @click="getRepos">Search Again</button>

        <p v-if="user">{{ user.login }},{{ user.bio }} has {{ repos.length }} repositories</p>

        <div class="repos__wrapper" v-if="repos">
          <div class="repos-item" v-for="repo in repos" :key="repo.id">
            <div class="repos-info">
              <a target="_blank" :href="repo.html_url">{{ repo.name }}</a>
              <span>{{ repo.stargazers_count }} ⭐</span>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import search from '@/components/Search'
import axios from 'axios'

export default {
  components:{
    search
  },
  data(){
    return{
      search:'',
      error:null,
      repos:null,
      user:null
    }
  },
  methods:{
    getRepos(){
      console.log(`get user ${this.search} repos`)
      // axios
      //     .get(`https://api.github.com/users/${this.search}/repos`)
      //     .then(res => {
      //       console.log(res)
      //       this.repos = res.data
      //       this.error = null
      //     })
      //     .catch(err => {
      //       console.log(err)
      //       this.repos=null
      //       this.error="Can't find this user"
      //     })
      axios.all([axios.get(`https://api.github.com/users/${this.search}/repos`),axios.get(`https://api.github.com/users/${this.search}`)])
      .then(axios.spread((...responses) => {
        console.log(responses[0])
        this.repos = responses[0].data
        this.user = responses[1].data
        this.error = null
        console.log(responses[1])
      }))
          .catch(err => {
            console.log(err)
            this.repos=null
            this.user=null
            this.error="Can't find this user"
          })
    }
  }
}
</script>

<style scoped>
.container{
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  flex-direction: column;
}
button{
  margin-top: 40px;
  margin-bottom: 40px;
}
.repos__wrapper{
  width: 400px;
  margin: 30px 0;
}
.repos-info{
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 10px 0;
  border-bottom: 1px solid #dbdbdb;
}
.error{
  margin-bottom: 40px;
}
</style>