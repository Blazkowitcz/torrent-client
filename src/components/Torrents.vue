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
                  <v-file-input
                    counter
                    multiple
                    truncate-length="13"
                  ></v-file-input>
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
          <v-simple-table @row-contextmenu="rightClicked">
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
                <tr v-for="torrent in torrents" :key="torrent.name">
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
        </v-col>
      </v-main>
    </v-app>
  </div>
</template>

<script>
export default {
  data() {
    return {
      torrents: [],
      loop: null,
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
    rightClicked() {
      alert("right clicked")
    }
  },
  mounted() {
    this.getTorrents();
    this.loop = setInterval(() => {
      this.getShortData();
    }, 3600);
  },
};
</script>