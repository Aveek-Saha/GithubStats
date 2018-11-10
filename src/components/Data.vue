<template>
  <div class="hello">

        <div class="row justify-content-center">
          <div class="col-lg-6">

            <div class="card bg-light shadow-sm mb-3 rounded" style="margin-top: 2%">
              
              <div class="card-body">
                <h5 class="card-title">Enter Details</h5>
                
                <form>
                  <div class="form-group">
                    <input type="text" class="form-control form-control-sm" v-model="username" 
                    id="username" placeholder="Your username" v-on:focusout="getRepos" 
                    v-on:keyup.enter="setFocus">
                  </div>

                  <div class="form-group">
                    <input type="text" class="form-control form-control-sm" v-model="repository" 
                    list="repos" id="repository" ref="search" placeholder="Repository name" 
                    v-on:keyup.enter="sendReq">
                    <datalist id="repos">
                      <option v-for="(repo , index) in repos" :key="index"> {{repo}} </option>
                    </datalist>
                  </div>
                </form>

                <button class="btn btn-outline-dark" v-on:click="sendReq">Submit</button>
              </div>
            </div>


            
        </div>
      </div>

      <div class="row justify-content-center">
          <div class="col-lg-6">

            <div class="spinner" v-if="loading" >
              <div class="rect1"></div>
              <div class="rect2"></div>
              <div class="rect3"></div>
              <div class="rect4"></div>
              <div class="rect5"></div>
              <div class="rect6"></div>
            </div>

            <h5 v-if="grandTotal && success" style="margin-bottom: 3%;"> 
              <i class="fas fa-download"></i>
              Total Downloads: {{grandTotal.toLocaleString()}} 
            </h5>
            
            <div class="card shadow-sm mb-3 rounded " style="margin-top: 2%; text-align:left" 
            v-if="success" v-for="(release , index) in releases" :key="index">

              <div class="card-header" v-bind:class="{ 'alert-warning': release.prerelease
              , 'alert-success': index == 0 }" >
                <h5 class="card-title" style="margin-bottom:0px"> 
                  <a v-bind:href="release.html_url" class="alert-link" style="color:inherit">
                  <i class="fas fa-tag"></i> {{release.tag_name}}</a> &nbsp;
                  <i class="fas fa-angle-right"></i> &nbsp;
                  <span class="text-muted h6"> 
                    {{release.total.toLocaleString()}} downloads
                  </span>
                </h5>
              </div>

              <div class="card-body" >

                <div class="h6" v-if="index == 0 || release.prerelease" >
                  <i class="fas fa-code-branch"></i> &nbsp;
                  <span class="badge badge-pill alert-success" v-if="index == 0">Latest</span>
                  <span class="badge badge-pill alert-warning" v-if="release.prerelease">
                    Prerelease</span>
                </div>
                <div class="h6">
                  <i class="fas fa-user-circle"></i> &nbsp;
                  <span class="small"><strong>Author- </strong> 
                    <a v-bind:href="release.author.html_url"> {{ release.author.login }} 
                      </a>
                  </span>
                  
                </div>
                <div class="h6">
                  <i class="far fa-calendar-alt"></i> &nbsp;
                  <span class="small"><strong>Published- </strong> {{ release.pubDate }}</span>
                  
                </div>
                <hr>

                <table class="table table-borderless table-sm">
                  <thead>
                    <tr>
                      <th scope="col">Name</th>
                      <th scope="col"> <i class="fas fa-download"></i> </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="( asset , index) in release.assets" :key="index">
                      <th scope="row" class="asset">{{asset.name}}</th>
                      <td>{{asset.download_count.toLocaleString()}} </td>
                    </tr>
                  </tbody>
                </table>
                
              </div>
            </div>

            <h5 v-if="!success" style="margin: 2%;">
              No repository found
            </h5>

            <h5 v-if="empty" style="margin: 2%;">
              No Releases
            </h5>

          </div>
      </div>

    </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Data',
  data() {
    return {
      username: "",
      repository: "",
      releases: [],
      repos: [],
      grandTotal: 0,
      success: true,
      empty: false,
      loading: false,
      url: "https://api.github.com"
    }
  },
  methods: {
    sendReq: function(){
      var that = this
      this.loading = true;
      this.empty = false;
      var options = { year: 'numeric', month: 'long', day: 'numeric' };
      axios.get(this.url + '/repos/' + this.username + "/" + this.repository + "/releases" )
        .then(function (response) {
          // console.log(response.data);
          var data = response.data
          // console.log(data);
          that.grandTotal = 0

          for (let i = 0; i < data.length; i++) {
            var total = 0
            data[i].total = 0
            for (let j = 0; j < data[i].assets.length; j++) {
              total += parseInt(data[i].assets[j].download_count)
            }
            data[i].total = total
            that.grandTotal += total

            var date = new Date(data[i].published_at)
            data[i].pubDate = date.toLocaleDateString("en-IN", options)
            // console.log(total);
          }

          that.releases = data
          that.success = true
          that.loading = false
          if(response.data.length === 0){
            that.empty = true
          }
          // console.log(data);          
        })
        .catch(function (error) {
          // console.log(error);
          that.success = false
          that.loading = false
          that.empty = false

        });
        
    },
    getRepos: function(){
      var that = this
      this.repos = []
      axios.get(this.url + '/users/' + this.username + "/repos" )
        .then(function (response) {
          // console.log(response.data);
          var data = response.data
          for (let i = 0; i < data.length; i++) {
            // console.log(data[i].name);
            that.repos.push(data[i].name)
          }
          
        })
        .catch(function (error) {
        });
    },
    setFocus: function()
    {
      this.$refs.search.focus();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

/* .card{
  background-color: #EDEDED
} */

.alert-warning{
  color: inherit
}
.alert-success{
  color: inherit
}

.spinner {
  margin: 100px auto;
  width: 60px;
  height: 50px;
  text-align: center;
  font-size: 10px;
}

.spinner > div {
  background-color: #333;
  height: 100%;
  width: 6px;
  display: inline-block;
  margin: 2px;
  
  -webkit-animation: sk-stretchdelay 1.2s infinite ease-in-out;
  animation: sk-stretchdelay 1.2s infinite ease-in-out;
}

.spinner .rect2 {
  -webkit-animation-delay: -1.1s;
  animation-delay: -1.1s;
}

.spinner .rect3 {
  -webkit-animation-delay: -1.0s;
  animation-delay: -1.0s;
}

.spinner .rect4 {
  -webkit-animation-delay: -0.9s;
  animation-delay: -0.9s;
}

.spinner .rect5 {
  -webkit-animation-delay: -0.8s;
  animation-delay: -0.8s;
}

.spinner .rect6 {
  -webkit-animation-delay: -0.7s;
  animation-delay: -0.7s;
}

@-webkit-keyframes sk-stretchdelay {
  0%, 40%, 100% { -webkit-transform: scaleY(0.4) }  
  20% { -webkit-transform: scaleY(1.0) }
}

@keyframes sk-stretchdelay {
  0%, 40%, 100% { 
    transform: scaleY(0.4);
    -webkit-transform: scaleY(0.4);
  }  20% { 
    transform: scaleY(1.0);
    -webkit-transform: scaleY(1.0);
  }
}

</style>
