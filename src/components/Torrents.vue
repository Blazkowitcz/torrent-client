<template>
  <div>
    <v-app>
      <v-app-bar app color="primary" dark>
        <div class="d-flex align-center">
          <v-img
            alt="Vuetify Logo"
            class="shrink mr-2"
            contain
            src="https://i.imgur.com/SKhdFP8.png"
            transition="scale-transition"
            width="40"
          />
          <h1>Tode</h1>
        </div>
        <v-spacer></v-spacer>
        <v-col cols="auto">
          <v-dialog transition="dialog-top-transition" max-width="600">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="info" v-bind="attrs" v-on="on">Add torrents</v-btn>
            </template>
            <template v-slot:default="dialog">
              <v-card>
                <v-toolbar color="primary" dark>Download torrents</v-toolbar>
                <v-card-text>
                  <vue-dropzone
                    ref="myVueDropzone"
                    id="dropzone"
                    :options="dropzoneOptions"
                    @vdropzone-complete="getTorrents"
                  ></vue-dropzone>
                </v-card-text>
                <v-card-actions class="justify-end">
                  <v-btn text @click="dialog.value = false">Close</v-btn>
                  <v-btn text @click="dialog.value = false">Validate</v-btn>
                </v-card-actions>
              </v-card>
            </template>
          </v-dialog>
        </v-col>
      </v-app-bar>
      <v-main>
        <v-col md="12">
          <v-layout column>
            <v-flex md6 style="overflow: auto">
              <v-simple-table
                :height="this.windowHeight / 2"
                fixed-header
                dense
              >
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Name</th>
                      <th class="text-left">Size</th>
                      <th class="text-left">Status</th>
                      <th class="text-left">Progress</th>
                      <th class="text-left">Down Speed</th>
                      <th class="text-left">Up Speed</th>
                      <th class="text-left>">Downloaded</th>
                      <th class="text-left>">Uploaded</th>
                      <th class="text-left">ETA</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="torrent in torrents"
                      :key="torrent.name"
                      @click="getTorrentDetail(torrent.infoHash)"
                    >
                      <td>{{ torrent.name }}</td>
                      <td>{{ torrent.size }}</td>
                      <td>{{ torrent.status }}</td>
                      <td>
                        <v-progress-linear
                          :color="torrent.status_color"
                          height="20"
                          :value="torrent.progress"
                        >
                          <template>
                            <strong>{{ Math.ceil(torrent.progress) }}%</strong>
                          </template>
                        </v-progress-linear>
                      </td>
                      <td>{{ torrent.download_speed }}</td>
                      <td>{{ torrent.upload_speed }}</td>
                      <td>{{ torrent.downloaded }}</td>
                      <td>{{ torrent.uploaded }}</td>
                      <td>{{ torrent.ETA }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-flex>
          </v-layout>
        </v-col>
      </v-main>
      <v-footer>
        <v-col md="12">
          <div>
            <v-btn color="info" @click="changePan('pan_general')"
              >General</v-btn
            >
            <v-btn color="info" @click="changePan('pan_content')"
              >Content</v-btn
            >
            <v-btn color="info" @click="changePan('pan_pairs')">Pairs</v-btn>
            <v-btn color="info" @click="changePan('pan_options')"
              >Options</v-btn
            >
          </div>
          <v-card
            elevation="2"
            outlined
            :height="this.windowHeight / 3"
            class="card-outter"
          >
            <div v-if="this.pans.pan_general">
              <v-row>
                <v-col md="4">
                  <span><b>Name : </b></span
                  ><span v-if="this.torrent_selected !== null"
                    >{{ this.torrent_selected.name.substring(0, 50) }}..</span
                  >
                </v-col>
                <v-col md="4">
                  <span><b>Downloaded : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.downloaded
                  }}</span>
                </v-col>
                <v-col md="4">
                  <span><b>Uploaded : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.uploaded
                  }}</span>
                </v-col>
              </v-row>
              <v-row>
                <v-col md="4">
                  <span><b>Tracker : </b></span
                  ><span v-if="this.torrent_selected !== null"
                    >{{
                      this.torrent_selected.announce.substring(0, 50)
                    }}..</span
                  >
                </v-col>
                <v-col md="4">
                  <span><b>Path : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.path
                  }}</span>
                </v-col>
                <v-col md="4">
                  <span><b>Ratio : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.ratio
                  }}</span>
                </v-col>
              </v-row>
              <v-row>
                <v-col md="4">
                  <span><b>Pieces : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.numberPieces +
                    " x " +
                    this.torrent_selected.pieceLength
                  }}</span>
                </v-col>
                <v-col md="4">
                  <span><b>Created : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.created
                  }}</span>
                </v-col>
                <v-col md="4">
                  <span><b>Created By : </b></span
                  ><span v-if="this.torrent_selected !== null">{{
                    this.torrent_selected.createdBy
                  }}</span>
                </v-col>
              </v-row>
            </div>
            <div v-if="this.pans.pan_pairs">
              <v-simple-table dense>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">Address</th>
                      <th class="text-left">Port</th>
                      <th class="text-left">Download Speed</th>
                      <th class="text-left">Upload Speed</th>
                      <th class="text-left">Downloaded</th>
                      <th class="text-left">Uploaded</th>
                    </tr>
                  </thead>
                  <tbody v-if="torrent_selected !== null">
                    <tr
                      v-for="peer in torrent_selected.peers"
                      :key="peer.address"
                    >
                      <td>{{ peer.address }}</td>
                      <td>{{ peer.port }}</td>
                      <td>{{ peer.download_speed }}</td>
                      <td>{{ peer.upload_speed }}</td>
                      <td>{{ peer.downloaded }}</td>
                      <td>{{ peer.uploaded }}</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </div>
            <div v-if="this.pans.pan_options">
              <v-row>
                <v-col md="1">
                  <p class="vertical-center">Move Torrent :</p>
                </v-col>
                <v-col md="9">
                  <v-text-field
                    hide-details="auto"
                    v-model="new_path"
                  ></v-text-field>
                </v-col>
                <v-col md="2">
                  <v-btn
                    color="info"
                    class="vertical-center"
                    @click="moveTorrent"
                    >Move</v-btn
                  >
                  <v-btn
                    color="info"
                    class="vertical-center"
                    @click="changeTorrentLocation"
                    >Change Location</v-btn
                  >
                </v-col>
              </v-row>
              <v-row>
                <v-col md="10" offset="1">
                  <v-divider></v-divider>
                </v-col>
              </v-row>
              <v-row>
                <v-layout justify-center>
                  <v-btn
                      color="warning"
                      @click="pauseTorrent"
                      v-if="torrent_selected.paused === false"
                      >Pause</v-btn
                    >
                    <v-btn
                      color="success"
                      @click="resumeTorrent"
                      v-if="torrent_selected.paused === true"
                      >Resume</v-btn
                    >
                    <v-btn color="info">Recheck</v-btn>
                    <v-btn color="error">Delete</v-btn>
                </v-layout>
              </v-row>
            </div>
            <div v-if="this.pans.pan_content">
              <v-flex md12 style="overflow: auto">
                <v-list-item-group>
                  <v-list
                    :max-height="windowHeight / 3"
                    v-if="this.torrent_selected !== null"
                  >
                    <v-list-item
                      v-for="file in torrent_selected.files"
                      :key="file.name"
                      >{{ file.name }}</v-list-item
                    >
                  </v-list>
                </v-list-item-group>
              </v-flex>
            </div>
          </v-card>
        </v-col>
      </v-footer>
    </v-app>
  </div>
</template>

<script>
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";

export default {
  components: {
    vueDropzone: vue2Dropzone,
  },
  data() {
    return {
      windowHeight: window.innerHeight,
      torrents: [],
      new_path: "",
      pans: {
        pan_general: true,
        pan_content: false,
        pan_pairs: false,
        pan_options: false,
      },
      torrent_selected: null,
      loop: null,
      dropzoneOptions: {
        url: "http://127.0.0.1:3000/add-torrent",
        thumbnailWidth: 150,
        maxFilesize: 1,
        headers: { "My-Awesome-Header": "header value" },
      },
    };
  },
  methods: {
    getTorrents: function () {
      fetch("http://127.0.0.1:3000/torrents", { mode: "cors" })
        .then((response) => response.json())
        .then((data) => {
          this.torrents = data;
          console.log(data);
        });
    },
    getShortData: function () {
      fetch("http://127.0.0.1:3000/torrents-short", { mode: "cors" })
        .then((response) => response.json())
        .then((data) => {
          this.updateData(data);
        });
    },
    getTorrentDetail: function (hash) {
      fetch("http://127.0.0.1:3000/torrents/" + hash, { mode: "cors" })
        .then((response) => response.json())
        .then((data) => {
          this.torrent_selected = data;
        });
    },
    updateData: function (data) {
      this.torrents.forEach((value) => {
        data.forEach((val) => {
          if (val.infoHash === value.infoHash) {
            value.download_speed = val.download_speed;
            value.upload_speed = val.upload_speed;
            value.progress = val.progress;
            value.status = val.status;
            value.status_color = val.status_color;
            value.uploaded = val.uploaded;
            value.downloaded = val.downloaded;
          }
        });
      });
    },
    moveTorrent: function () {
      fetch("http://127.0.0.1:3000/torrent-move", {
        mode: "cors",
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          hash: this.torrent_selected.infoHash,
          path: this.new_path,
        }),
      });
    },
    changeTorrentLocation: function () {
      fetch("http://127.0.0.1:3000/torrent-change-location", {
        mode: "cors",
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          hash: this.torrent_selected.infoHash,
          path: this.new_path,
        }),
      });
    },
    changePan: function (pan) {
      for (const [key] of Object.entries(this.pans)) {
        this.pans[key] = false;
        console.log(this.pans[key]);
      }
      this.pans[pan] = true;
    },
    uploadTorrent: function () {},
    rightClicked() {
      alert(this.new_path);
    },
  },
  mounted() {
    this.getTorrents();
    this.loop = setInterval(() => {
      this.getShortData();
    }, 3600);
  },
};
</script>
<style>
.vertical-center {
  margin-top: 20px;
}
.card-outter {
  position: relative;
  padding-bottom: 50px;
}
</style>