<template>
  <div>
    <div
      class="row filters"
      style="
        margin: 0;
        position: fixed;
        bottom: 0;
        width: 100%;
        left: 0;
        z-index: 999;
      "
    >
      <div class="col">
        <b-form-input
          v-model="filter_address"
          placeholder="Filter by address"
        ></b-form-input>
      </div>
      <div class="col">
        <b-form-input
          v-model="filter_protocol"
          placeholder="Filter by protocol"
        ></b-form-input>
      </div>
      <div class="col">
        <b-form-input
          v-model="filter_collection"
          placeholder="Filter by collection"
        ></b-form-input>
      </div>
      <div class="col">
        <b-form-input
          v-model="filter_refid"
          placeholder="Filter by reference"
        ></b-form-input>
      </div>
      <div class="col">
        <b-form-input
          v-model="filter_uuid"
          placeholder="Filter by UUID"
        ></b-form-input>
      </div>
      <div class="col">
        <div
          v-on:click="filterData"
          style="width: 100%"
          class="btn btn-primary"
        >
          FILTER
        </div>
      </div>
    </div>
    <div class="container" style="padding-top: 10px">
      <div class="col-12">
        <div v-if="last.length === 0" style="padding: 45vh 0">
          Loading data, please wait...
        </div>
        <div v-if="unconfirmedTxs.length > 0">
          <h3 style="margin-top: 30px">Unconfirmed transactions</h3>
          <div v-for="data in unconfirmedTxs" v-bind:key="data.uuid">
            <div
              class="card"
              style="width: 100%; margin-top: 20px"
              v-if="data.block === undefined || data.block === null"
            >
              <div class="card-body">
                <h5
                  class="card-title"
                  v-if="
                    data.title !== undefined &&
                    data.title !== 'undefined' &&
                    data.title !== ''
                  "
                >
                  {{ data.title }}
                </h5>
                <h5
                  class="card-title"
                  v-if="
                    data.refID !== undefined &&
                    data.refID !== 'undefined' &&
                    data.refID !== ''
                  "
                >
                  {{ data.refID }}
                </h5>
                <div v-if="data.mime && data.is_file">
                  <img
                    :src="idanode + '/ipfs/' + data"
                    width="100%"
                    v-if="data.mime.type === 'image'"
                  />
                  IPFS hash:
                  <a :href="idanode + '/ipfs/' + data" target="_blank">{{
                    data
                  }}</a
                  ><br />
                  <span v-if="data.mime.mime"
                    >File Type: {{ data.mime.mime.toUpperCase() }}<br
                  /></span>
                </div>
                <p v-if="data.is_file === false" class="card-text">
                  <span v-if="data.protocol"
                    ><b>{{ data.protocol }}</b></span
                  >{{ data }}
                </p>
                <div style="text-align: center">
                  UUID is <a :href="'/#/uuid/' + data.uuid">{{ data.uuid }}</a
                  ><br />
                  Written by
                  <a :href="'/#/address/' + data.address">{{ data.address }}</a>
                </div>
                <hr />
                <div style="text-align: center">
                  <a
                    :href="'/#/uuid/' + data.uuid"
                    style="margin: 10px"
                    class="btn btn-primary"
                    >Show details</a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
        <div v-if="last.length > 0">
          <h3 style="margin-top: 30px">Confirmed transactions</h3>
          <div v-for="data in last" v-bind:key="data.uuid">
            <div
              class="card"
              style="width: 100%; margin-top: 20px"
              v-if="data.block > 0"
            >
              <div class="card-body">
                <h5
                  class="card-title"
                  v-if="
                    data.title !== undefined &&
                    data.title !== 'undefined' &&
                    data.title !== ''
                  "
                >
                  {{ data.title }}
                </h5>
                <h5
                  class="card-title"
                  v-if="
                    data.refID !== undefined &&
                    data.refID !== 'undefined' &&
                    data.refID !== ''
                  "
                >
                  {{ data.refID }}
                </h5>
                <div v-if="data.mime && data.is_file">
                  <img
                    :src="idanode + '/ipfs/' + data"
                    width="100%"
                    v-if="data.mime.type === 'image'"
                  />
                  IPFS hash:
                  <a :href="idanode + '/ipfs/' + data" target="_blank">{{
                    data
                  }}</a
                  ><br />
                  <span v-if="data.mime.mime"
                    >File Type: {{ data.mime.mime.toUpperCase() }}<br
                  /></span>
                </div>
                <div v-if="data.mime && data.protocol === 'documenta://'">
                  <img
                    :src="data.documentaURL"
                    width="100%"
                    v-if="data.mime.type === 'image'"
                  />
                  IPFS hash:
                  <a :href="idanode + '/ipfs/' + data" target="_blank">{{
                    data
                  }}</a
                  ><br />
                  <span v-if="data.mime.mime"
                    >File Type: {{ data.mime.mime.toUpperCase() }}<br
                  /></span>
                </div>
                <p v-if="data.is_file === false" class="card-text">
                  <span v-if="data.protocol"
                    ><b>{{ data.protocol }}</b></span
                  >{{ data }}
                </p>
                <div style="text-align: center">
                  UUID is <a :href="'/#/uuid/' + data.uuid">{{ data.uuid }}</a
                  ><br />
                  Written by
                  <a :href="'/#/address/' + data.address">{{ data.address }}</a>
                  at block
                  <a :href="'/#/block/' + data.block">{{ data.block }}</a>
                </div>
                <hr />
                <div style="text-align: center">
                  <a
                    :href="'/#/uuid/' + data.uuid"
                    style="margin: 10px; font-size: 12px; padding: 5px 10px"
                    class="btn btn-primary"
                    >Show details</a
                  >
                  <a
                    v-if="data.is_file === true"
                    :href="idanode + '/ipfs/' + data"
                    target="_blank"
                    style="margin: 10px"
                    class="btn btn-primary"
                    >Download file</a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import bans from "../bans.json";
import axios from "axios";
const ScryptaCore = require('@scrypta/core')

export default {
  name: "Explorer",
  data() {
    return {
      idanode: "",
      axios: axios,
      last: [],
      bans: bans,
      unconfirmedTxs: [],
      filter_address: "",
      filter_uuid: "",
      filter_collection: "",
      filter_refid: "",
      filter_protocol: "",
      scrypta: new ScryptaCore(true)
    };
  },
  async mounted() {
    const app = this;
    app.scrypta.staticnodes = true
    app.filterData()
  },
  methods: {
    async filterData() {
      const app = this;
      app.last = [];
      let filters = {};
      let hasFilter = false;
      if (app.filter_protocol !== "") {
        filters["protocol"] = app.filter_protocol;
        hasFilter = true;
      }
      if (app.filter_collection !== "") {
        filters["collection"] = app.filter_collection;
        hasFilter = true;
      }
      if (app.filter_refid !== "") {
        filters["refID"] = app.filter_refid;
        hasFilter = true;
      }
      if (app.filter_uuid !== "") {
        filters["uuid"] = app.filter_uuid;
        hasFilter = true;
      }
      if (app.filter_address !== "") {
        filters["address"] = app.filter_address;
        hasFilter = true;
      }
      var readreturn = [];
      if (!hasFilter) {
        readreturn = await app.scrypta.post("/read");
      } else {
        readreturn = await app.scrypta.post("/read", filters);
      }
      for (let i in readreturn.data) {
        if (
          readreturn.data[i].uuid !== undefined &&
          app.bans.uuids.indexOf(readreturn.data[i].uuid) === -1
        ) {
          if (readreturn.data[i].is_file === true) {
            let mime = await app.scrypta.get(
              "/ipfs/type/" + readreturn.data[i].data
            );
            readreturn.data[i].mime = mime.data;
            app.last.push(readreturn.data[i]);
          } else {
            app.last.push(readreturn.data[i]);
          }
          if (
            readreturn.data[i].block === undefined ||
            readreturn.data[i].block === null
          ) {
            app.unconfirmedTxs.push(readreturn.data[i]);
          }
        }
      }
    },
  },
};
</script>

<style>
.card-title {
  font-size: 12px;
}
.card-body {
  font-size: 11px;
  text-align: center !important;
}
.filters {
  padding-top: 0px;
  font-size: 13px;
}
.filters .col {
  padding: 0;
  margin: 0;
}
.filters input {
  color: #fff;
  background: #222;
  font-size: 13px;
  border-radius: 0;
}
.filters .btn {
  font-size: 13px;
  border-radius: 0px;
}
</style>