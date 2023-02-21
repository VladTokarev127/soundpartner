<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <main>
    <div class="content-container">
      <h2 class="mb-4">
        Ваши заведения
      </h2>

      <v-row
        v-if="loadingStations"
      >
        <div
          class="d-flex justify-center align-center pa-5"
          style="min-height: 245px;"
        >
          <v-progress-circular
            indeterminate
            color="#2256F6"
          />
        </div>
      </v-row>

      <v-row v-if="!loadingStations && objects && objects.length">
        <v-col
          v-for="r in objects"
          :key="r.id"
          cols="12"
          lg="4"
        >
          <div
            v-if="r"
            class="rest-card"
          >
            <div>
              <h4 class="mb-2">
                {{ r.station?.name }}
              </h4>
              <h4 v-if="r.now_playing?.song">
                Сейчас играет “{{ r.now_playing.song.text }}”
              </h4>
            </div>
            <div class="d-flex justify-end">
              <NuxtLink :to="{ path: `objects/${r.station?.id}` }">
                <v-btn
                  class="simple-btn"
                  depressed
                >
                  Подробнее
                </v-btn>
              </NuxtLink>
            </div>
          </div>
        </v-col>
      </v-row>

      <div class="d-flex justify-space-between align-center mt-12 mb-4">
        <h2>
          Твой плейлист
        </h2>
        <v-btn
          color="#2256F6"
          text
          style="font-weight: 600; font-size: 18px; line-height: 22px; text-transform: none; letter-spacing: normal;"
        >
          Смотреть все
        </v-btn>
      </div>

			<v-row>
        <v-col cols="12">
          <v-simple-table>
            <template #default>
              <tbody>
                <tr
                  v-for="(item, index) in items"
                  :key="item.name + index + item.number"
                >
                  <td width="10">
                    {{ '0' + (index + 1) }}
                  </td>
                  <td
                    width="50"
                    style="position: relative;"
                  >
                    <v-img
                      min-height="50"
                      min-width="50"
                      max-height="50"
                      max-width="50"
                      :src="item.avatar"
                      style="background: #D9D9D9; border-radius: 5px;"
                    />
                    <div
                      class="avatar-hover"
                      @click="play(index, showLiked ? liked : notLiked)"
                    >
                      <v-icon>mdi-play</v-icon>
                    </div>
                  </td>
                  <td width="200">
                    {{ item.title }}
                  </td>
                  <td>{{ item.artist }}</td>
                  <td width="160">
                    {{ item.plays }}
                  </td>
                  <td width="100">
                    {{ item.duration }}
                  </td>
                  <td width="100">
                    <v-icon
                      v-if="item.liked"
                      color="#2256F6"
                      @click="item.liked = !item.liked"
                    >
                      mdi-heart
                    </v-icon>
                    <v-icon
                      v-else
                      color="#2256F6"
                      @click="item.liked = !item.liked"
                    >
                      mdi-heart-outline
                    </v-icon>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-col>
      </v-row>

      <div class="d-flex justify-space-between align-center mt-12 mb-4">
        <h2>
          Популярно для тебя
        </h2>
        <v-btn
          color="#2256F6"
          text
          style="font-weight: 600; font-size: 18px; line-height: 22px; text-transform: none; letter-spacing: normal;"
        >
          Смотреть все
        </v-btn>
      </div>
			<v-row>
        <v-col cols="12">
          <v-simple-table>
            <template #default>
              <tbody>
                <tr
                  v-for="(item, index) in items"
                  :key="item.name + index + item.number"
                >
                  <td width="10">
                    {{ '0' + (index + 1) }}
                  </td>
                  <td
                    width="50"
                    style="position: relative;"
                  >
                    <v-img
                      min-height="50"
                      min-width="50"
                      max-height="50"
                      max-width="50"
                      :src="item.avatar"
                      style="background: #D9D9D9; border-radius: 5px;"
                    />
                    <div
                      class="avatar-hover"
                      @click="play(index, showLiked ? liked : notLiked)"
                    >
                      <v-icon>mdi-play</v-icon>
                    </div>
                  </td>
                  <td width="200">
                    {{ item.title }}
                  </td>
                  <td>{{ item.artist }}</td>
                  <td width="160">
                    {{ item.plays }}
                  </td>
                  <td width="100">
                    {{ item.duration }}
                  </td>
                  <td width="100">
                    <v-icon
                      v-if="item.liked"
                      color="#2256F6"
                      @click="item.liked = !item.liked"
                    >
                      mdi-heart
                    </v-icon>
                    <v-icon
                      v-else
                      color="#2256F6"
                      @click="item.liked = !item.liked"
                    >
                      mdi-heart-outline
                    </v-icon>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-col>
      </v-row>
    </div>
  </main>
</template>

<script>
export default {
  layout: 'lk',
  data () {
    return {
      loadingStations: true,
      showLiked: true,
      items: [
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          source: '../audio/millions.mp3',
          number: '01',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          source: '../audio/millions.mp3',
          number: '02',
          liked: true
        },
        {
          artist: 'DJ Shadow, Mos Def',
          title: 'Six Days',
          avatar: 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTWy15ez24PqDSOEn7zfVZTAz-U22aDiQ5-2A&usqp=CAU',
          plays: '4 300 124',
          duration: '3:53',
          source: '../audio/six-days.mp3',
          number: '03',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          source: '../audio/millions.mp3',
          number: '04',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          source: '../audio/millions.mp3',
          number: '05',
          liked: true
        },
      ],
      recommendedItems: [
        {
          artist: 'Alan Walker',
          title: 'The Drum',
          avatar: 'http://s3.ap-south-1.amazonaws.com/discovery-prod-zion/zion/1671119553458-alan_walker.jpg',
          plays: '8 796 542',
          duration: '3:09',
          source: '../audio/the-drum.mp3',
          number: '01',
          liked: false
        },
        {
          artist: 'Alan Walker',
          title: 'The Drum',
          avatar: 'http://s3.ap-south-1.amazonaws.com/discovery-prod-zion/zion/1671119553458-alan_walker.jpg',
          plays: '8 796 542',
          duration: '3:09',
          source: '../audio/the-drum.mp3',
          number: '02',
          liked: false
        },
        {
          artist: 'Alan Walker',
          title: 'The Drum',
          avatar: 'http://s3.ap-south-1.amazonaws.com/discovery-prod-zion/zion/1671119553458-alan_walker.jpg',
          plays: '8 796 542',
          duration: '3:09',
          source: '../audio/the-drum.mp3',
          number: '03',
          liked: false
        },
        {
          artist: 'Alan Walker',
          title: 'The Drum',
          avatar: 'http://s3.ap-south-1.amazonaws.com/discovery-prod-zion/zion/1671119553458-alan_walker.jpg',
          plays: '8 796 542',
          duration: '3:09',
          source: '../audio/the-drum.mp3',
          number: '04',
          liked: false
        },
        {
          artist: 'Alan Walker',
          title: 'The Drum',
          avatar: 'http://s3.ap-south-1.amazonaws.com/discovery-prod-zion/zion/1671119553458-alan_walker.jpg',
          plays: '8 796 542',
          duration: '3:09',
          source: '../audio/the-drum.mp3',
          number: '05',
          liked: false
        },
      ],
    }
  },
  mounted () {
    this.getStations();
  },
  methods: {
    getStations () {
      this.$axios.get('stations')
        .then((result) => {
          if (result?.data) {
            this.objects = [ ...result.data ];

            this.getStationsInfo();
          }
        })
        .catch((err) => {
          console.error(err);
        });
    },
    getStationsInfo () {
      for (const [index, r] of this.objects.entries()) {
        this.$axios.get(`nowplaying/${r.id}`)
          .then((result) => {
            if (result?.data) {
              this.objects[index] = result.data;
            }

            if (index === this.objects.length - 1) {
              setTimeout(() => {
                this.loadingStations = false;
              }, 1500)
            }
          })
          .catch((err) => {
            console.error(err);
          });
      }
    }
  },
}
</script>

<style>
  .v-data-table > .v-data-table__wrapper > table > tbody > tr > td {
    padding-bottom: 10px !important;
    padding-top: 10px !important;
  }

  .avatar-hover {
    display: none;
    justify-content: center;
    align-items: center;
    width: calc(100% - 32px);
    height: calc(100% - 20px);
    position: absolute;
    border-radius: 5px;
    cursor: pointer;
    top: 10px;
    left: 16px;
    background: linear-gradient(120deg, rgba(24,229,237,0.45) 0%, rgba(156,29,223,0.45) 32%, rgba(46,193,236,0.45) 100%);
  }

  .avatar-hover i.v-icon {
    color: #FFFFFF;
    font-size: 34px;
  }

  .v-data-table > .v-data-table__wrapper > table > tbody > tr:hover > td > .avatar-hover {
    display: flex;
  }
</style>

<style lang="scss" scoped>
  h2 {
    font-weight: 600;
    font-size: 30px;
    line-height: 37px;
    color: #000000;
  }

  .simple-btn {
    background: #FFFFFF;
    border-radius: 10px;
    padding: 6px 17px;
    font-weight: 600;
    font-size: 18px;
    line-height: 22px;
    color: #2256F6;
    text-transform: none;
  }

  .rest-card {
    height: 222px;
    background: #2256F6;
    border-radius: 10px;
    padding: 20px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    h4 {
      font-weight: 600;
      font-size: 18px;
      line-height: 22px;
      color: #FFFFFF;
    }
  }
</style>
