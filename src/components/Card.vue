<template>
    <div class="card shadow-sm mb-3 rounded " style="margin-top: 2%; text-align:left">

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
</template>

<script>

export default {
  name: 'Card',
  props: ['release', 'index'],
}
</script>

<style scoped>

.alert-warning{
  color: inherit
}
.alert-success{
  color: inherit
}
</style>
