<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <div v-if="last.length === 0" style="padding:45vh 0">
          Loading data, please wait...
        </div>
        <div v-if="last.length > 0">
          <h5 style="margin-top:20px">Written data for {{ $route.params.address }}</h5>
        </div>
        <div v-for="data in last" v-bind:key="data.uuid">
          <div class="card" style="width: 100%; margin-top:20px">
            <div class="card-body">
              <h5 class="card-title" v-if="data.title !== undefined && data.title !== 'undefined' && data.title !== ''">{{ data.title }}</h5>
              <h5 class="card-title" v-if="data.refID !== undefined && data.refID !== 'undefined' && data.refID !== ''">{{ data.refID }}</h5>
              <div v-if="data.mime">
                <img :src="idanode + '/ipfs/' + data.data" width="100%" v-if="data.mime.type === 'image'">
                IPFS hash: <a :href="idanode + '/ipfs/' + data.data" target="_blank">{{data.data}}</a><br>
                <span v-if="data.mime.mime">File Type: {{ data.mime.mime.toUpperCase() }}<br></span>
              </div>
              <p v-if="data.is_file === false" class="card-text">{{ data.data }}</p>
              <div style="text-align:center">
                UUID is <a :href="'/#/uuid/' + data.uuid">{{ data.uuid }}</a><br>
                Written by <a :href="'/#/address/' + data.address">{{ data.address }}</a> 
                at block <a :href="'/#/block/' + data.block">{{ data.block }}</a>
              </div>
              <hr>
              <a :href="'/#/uuid/' + data.uuid" class="btn btn-primary" style="margin: 10px">Show details</a>
              <a v-if="data.is_file === true" :href="idanode + '/ipfs/' + data.data" target="_blank" style="margin: 10px" class="btn btn-primary">Download file</a>
            </div>
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
      last: []
    }
  },
  async mounted() {
    const app = this
    app.idanode = await window.ScryptaCore.connectNode()
    let check = await app.axios.get(app.idanode + '/wallet/getinfo')
    if(check.data.blocks > 0){
      let readreturn = await app.axios.post(app.idanode + '/read', {
        address: app.$route.params.address
      })
      for(let i in readreturn.data.data){
        if(readreturn.data.data[i].uuid !== undefined){
          if(readreturn.data.data[i].is_file === true){
            let mime = await app.axios.get(app.idanode + '/ipfs/type/' + readreturn.data.data[i].data).catch(err => {
              alert('There\'s an error on IdaNode, please retry!')
            })
            readreturn.data.data[i].mime = mime.data.data
            app.last.push(readreturn.data.data[i])
          }else{
            app.last.push(readreturn.data.data[i])
          }
        }
      }
    }
  }
}
</script>