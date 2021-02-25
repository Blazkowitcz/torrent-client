<template>
  <div>
    <v-col md="12">
      <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">Name</th>
              <th class="text-left">Size</th>
              <th class="text-left">Status</th>
              <th class="text-left">Progress</th>
              <th class="text-left">Down Speed</th>
              <th class="text-left">Up Speed</th>
              <th class="text-left">ETA</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="torrent in torrents" :key="torrent.name">
              <td>{{ torrent.name }}</td>
              <td>{{ torrent.size }}</td>
              <td>{{ torrent.status }}</td>
              <td>
                <v-progress-linear color="success" height="20" :value="torrent.progress">
                  <template>
                    <strong>{{ Math.ceil(torrent.progress) }}%</strong>
                  </template>
                </v-progress-linear>
              </td>
              <td>{{ torrent.download_speed }}</td>
              <td>{{ torrent.upload_speed }}</td>
              <td>{{ torrent.ETA }}</td>
              <td>
                <v-btn @click="download(torrent.id, torrent.slug)">
                  <v-icon color="#22b598">mdi-download-box-outline</v-icon>
                </v-btn>
              </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </v-col>
  </div>
</template>

<script>
export default {
  data() {
    return {
      torrents: [],
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
  },
  mounted() {
    this.getTorrents();
  },
};
</script>