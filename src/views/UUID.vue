<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <h5 style="margin-top:20px">Proof for {{ $route.params.uuid }}</h5>
        <div v-if="isLoading" style="padding:45vh 0 0 0">
          Loading data, please wait...
        </div>
        <div v-if="!isLoading" class="card" style="width: 100%; margin-top:20px">
          <div class="card-body">
            <h5 class="card-title" v-if="data.title !== undefined && data.title !== 'undefined' && data.title !== ''">{{ data.title }}</h5>
            <h5 class="card-title" v-if="data.refID !== undefined && data.refID !== 'undefined' && data.refID !== ''">{{ data.refID }}</h5>
            <div v-if="data.mime">
              <img :src="idanode + '/ipfs/' + data.data" width="100%" v-if="data.mime.type === 'image'">
              IPFS hash: <a :href="idanode + '/ipfs/' + data.data" target="_blank">{{data.data}}</a><br>
              <span v-if="data.mime.mime">File Type: {{ data.mime.mime.toUpperCase() }}<br></span>
            </div>
            <p v-if="data.is_file === false" class="card-text">{{ data.data }}</p>
            UUID is <a :href="'/#/uuid/' + data.uuid">{{ data.uuid }}</a><br>
            Written by <a :href="'/#/address/' + data.address">{{ data.address }}</a> 
            at block <a :href="'/#/block/' + data.block">{{ data.block }}</a><br>
            Served by <a :href="idanode" target="_blank">{{ idanode }}</a> 
            <br>
          </div>
        </div>
        <br>
        <h5>Raw data</h5>
        <div class="card" style="width: 100%; margin-top:20px">
            <div class="card-body">
              <pre style="text-align:left">{{ data }}</pre>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Explorer',
  data () {
    return {
      idanode: 'https://idanodejs01.scryptachain.org',
      axios: axios,
      isLoading: true,
      data: {}
    }
  },
  async mounted() {
    const app = this
    let check = await app.axios.get(app.idanode + '/wallet/getinfo').catch(err => {
      console.log(err)
      alert('There\'s an error on IdaNode, please retry!')
    })
    if(check.data.blocks > 0){
      let readreturn = await app.axios.post(app.idanode + '/read', { uuid: app.$route.params.uuid }).catch(err => {
        console.log(err)
        alert('There\'s an error on IdaNode, please retry!')
      })
      for(let i in readreturn.data.data){
        if(readreturn.data.data[i].uuid !== undefined){
          if(readreturn.data.data[i].is_file === true){
            let mime = await app.axios.get(app.idanode + '/ipfs/type/' + readreturn.data.data[i].data).catch(err => {
              console.log(err)
              alert('There\'s an error on IdaNode, please retry!')
            })
            readreturn.data.data[i].mime = mime.data.data
            app.data = readreturn.data.data[i]
          }else{
            app.data = readreturn.data.data[i]
          }
          delete app.data['id']
        }
      }
      app.isLoading = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>