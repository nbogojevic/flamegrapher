<template>
<div>
    <b-row>
        <b-col>
            <b-table striped hover fixed :items="items" :fields="fields">
            <template slot="state" slot-scope="row">
              <b-progress :value="100" variant="secondary" :animated="true" class="mb-3" v-show="row.item.loading"></b-progress>
              <div v-show="!row.item.loading">{{row.item.state}}</div>
            </template>
            <template slot="recording" slot-scope="row">
                <a :href="`#/flames/${row.item.pid}/${row.item.recording}`" v-if="row.item.hasDump">
                {{row.item.recording}} (view)
                </a>
                <div v-else>
                {{row.item.recording}}
                </div>
            </template>
            <template slot="actions" slot-scope="row">
                <b-button-group size="sm">
                  <b-btn :disabled="row.item.state === 'Recording' || row.item.state === 'Dumped'" @click="start(row.item)">Start</b-btn>
                  <b-btn :disabled="row.item.state === 'Not recording'" @click="stop(row.item)">Stop</b-btn>
                  <b-btn :disabled="row.item.state === 'Not recording'" @click="dump(row.item)">Dump</b-btn>
                </b-button-group>
            </template>
            </b-table>
        </b-col>
    </b-row>
    <b-row class="text-left">
        <b-col>
          <a :href="`#/dumps`">
            View all available dumps
          </a>
        </b-col>
    </b-row>
</div>
</template>
<script>
import axios from "axios";
export default {
  name: "home",
  data () {
    return {
      fields: [
        {
          key: "pid",
          label: "PID",
          sortable: true
        },
        {
          key: "name",
          label: "Process name",
          sortable: true
        },
        {
          key: "state",
          label: "State",
          sortable: true
        },
        {
          key: "recording",
          label: "Recording #"
        },
        "actions"
      ]
    };
  },
  props: ["items"],
  methods: {
    start: function (item) {
      // Needs to call this.$set because loading is being dinamically added
      // and not reactive by default.
      this.$set(item, "loading", true);
      axios
        .get("/api/start/" + item.pid)
        .then(response => {
          item.state = response.data.state;
          item.recording = response.data.recording;
          item.loading = false;
        })
        .catch(e => {
          console.log(e);
        });
    },
    stop: function (item) {
      this.$set(item, "loading", true);
      axios
        .get("/api/stop/" + item.pid + "/" + item.recording)
        .then(response => {
          item.state = response.data.state;
          item.loading = false;
        })
        .catch(e => {
          console.log(e);
        });
    },
    dump: function (item) {
      this.$set(item, "loading", true);
      axios
        .get("/api/dump/" + item.pid + "/" + item.recording)
        .then(response => {
          item.state = response.data.state;
          this.$set(item, "hasDump", true);
          item.loading = false;
        })
        .catch(e => {
          console.log(e);
        });
    }
  }
};
</script>
