<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <v-app>
    <v-app-bar
      fixed
      app
      class="app-header"
    >
      <div class="logo">
        <NuxtLink :to="{ path: '/' }">
          <img
            src="../static/images/logo-horizontal.png"
            alt="SOUND PARTNER"
            @click="scrollTo('top')"
          >
        </NuxtLink>
      </div>

      <v-text-field
        placeholder="Поиск музыки, альбома, артиста или заведения"
        solo
        dense
        hide-details
        class="search-field"
      />
    </v-app-bar>

    <div
      class="d-flex"
      style="padding-top: 100px;"
    >
      <v-navigation-drawer permanent>
        <v-list-item>
          <v-list-item-content>
            <v-list-item-title class="nav-heading">
              Обзор музыки
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list
          dense
          nav
        >
          <div
            v-for="item in items1"
            :key="item.title"
          >
            <NuxtLink
              :to="{ path: item.route }"
              class="d-flex"
            >
              <v-list-item
                class="nav-item"
                link
              >
                <v-list-item-icon>
                  <v-icon>{{ item.icon }}</v-icon>
                </v-list-item-icon>

                <v-list-item-content>
                  <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </nuxtlink>
          </div>
        </v-list>

        <v-list-item>
          <v-list-item-content>
            <v-list-item-title class="nav-heading">
              Обзор заведения
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list
          dense
          nav
        >
          <div
            v-for="item in items2"
            :key="item.title"
          >
            <NuxtLink
              :to="{ path: item.route }"
              class="d-flex"
            >
              <v-list-item
                class="nav-item"
                link
              >
                <v-list-item-icon>
                  <v-icon>{{ item.icon }}</v-icon>
                </v-list-item-icon>

                <v-list-item-content>
                  <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </nuxtlink>
          </div>
        </v-list>

        <v-list-item>
          <v-list-item-content>
            <v-list-item-title class="nav-heading">
              Настройки
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list
          dense
          nav
        >
          <div
            v-for="item in items3"
            :key="item.title"
          >
            <NuxtLink
              :to="{ path: item.route }"
              class="d-flex"
            >
              <v-list-item
                class="nav-item"
                link
              >
                <v-list-item-icon>
                  <v-icon>{{ item.icon }}</v-icon>
                </v-list-item-icon>

                <v-list-item-content>
                  <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </nuxtlink>
          </div>

          <v-list-item
            class="nav-item"
            link
            @click="logout"
          >
            <v-list-item-icon>
              <v-icon>mdi-logout-variant</v-icon>
            </v-list-item-icon>

            <v-list-item-content>
              <v-list-item-title>Выход</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-navigation-drawer>

      <v-main style="padding-top: 0;">
        <v-container>
          <Nuxt />
        </v-container>
      </v-main>

      <v-navigation-drawer
        v-if="showSidebar"
        right
        permanent
        width="400"
      >
				<v-icon
					class="close-sidebar"
					color="#2256F6"
					@click="closeSidebar"
				>
					mdi-close
				</v-icon>
        <v-list class="music-player">
          <v-list-item class="mb-4">
            <v-list-item-title>
              <h2>Следующая песня</h2>
            </v-list-item-title>
          </v-list-item>

					<v-virtual-scroll
						v-if="currentTracks"
						:items="currentTracks"
						height="214"
						item-height="76"
					>
					<template v-slot:default="{ item }">
						<v-list-item :key="item.title">
							<v-list-item-avatar
								min-height="56"
								min-width="56"
								max-height="56"
								max-width="56"
								style="background: #D9D9D9; border-radius: 5px;"
								>
								<v-img
									min-height="56"
									min-width="56"
									max-height="56"
									max-width="56"
									:src="item.avatar"
								/>
							</v-list-item-avatar>
							<v-list-item-content>
								<v-list-item-title>{{ item.title }}</v-list-item-title>
								<v-list-item-subtitle>{{ item.artist }}</v-list-item-subtitle>
							</v-list-item-content>
							<v-list-item-action>
								<v-btn icon>
									<v-icon color="grey lighten-1">
										mdi-dots-vertical
									</v-icon>
								</v-btn>
							</v-list-item-action>
						</v-list-item>
					</template>
					</v-virtual-scroll>

          <v-list-item class="mt-4">
            <Player
							v-if="currentTracks"
              class="mb-5"
              wide
              autoplay
              :tracks="currentTracks"
							:currentSound="currentTrack"
							:key="componentKey"
            />
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
    </div>
  </v-app>
</template>

<script>
import Player from '@/components/Player.vue'
import { Howl } from 'howler'

export default {
  name: 'LKLayout',
  components: {
    Player
  },
  data () {
    return {
      showSidebar: false,
			currentTracks: null,
			currentTrack: 0,
			componentKey: 0,
			sound: null,
      items1: [
        { title: 'Главная', icon: 'mdi-home', route: '/dashboard' },
        { title: 'Обзор', icon: 'mdi-view-dashboard', route: '/review' },
        { title: 'Альбомы', icon: 'mdi-folder-play', route: '/albums' },
        { title: 'Понравилось', icon: 'mdi-heart', route: '/favourite' },
        { title: 'Не понравилось', icon: 'mdi-heart-off', route: '/disliked' },
      ],
      items2: [
        { title: 'Мои заведения', icon: 'mdi-home-city', route: '/objects' },
        { title: 'Мои плейлисты', icon: 'mdi-playlist-music', route: '/playlists' },
        { title: 'Мои файлы', icon: 'mdi-file-multiple', route: '/files' },
      ],
      items3: [
        { title: 'Мой аккаунт', icon: 'mdi-cog', route: '/account' },
      ],
      tracks: [
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          number: '01',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          number: '02',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          number: '03',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          number: '04',
          liked: true
        },
        {
          artist: 'Always Never',
          title: 'Millions',
          avatar: 'https://nikkur.ru/wp-content/uploads/2019/11/always-never-300x300.jpg',
          plays: '8 096 542',
          duration: '3:58',
          number: '05',
          liked: true
        },
      ]
    }
  },
  created() {
    this.$nuxt.$on('change-song', (index, tracks) => {
      this.changeTracks(tracks);
      this.changeTrack(index);
			this.showSidebar = true;
			this.forceRerender();
      console.log('change track event handle')
    })

    this.$nuxt.$on('hide-sidebar', () => {
      this.showSidebar = false;
			Howler.unload();
    })

    this.$nuxt.$on('show-sidebar', () => {
      this.showSidebar = true;
    })
  },
  methods: {
		changeSidebar () {
			this.showSidebar = !this.showSidebar;
		},
		closeSidebar () {
			this.showSidebar = false;
			Howler.unload();
		},
    changeTracks (tracks) {
      this.currentTracks = tracks;
    },
		changeTrack (index) {
      this.currentTrack = index;
    },
		forceRerender() {
      this.componentKey += 1;  
    },
    logout () {
      console.info('LOGOUT');
    }
  }
}
</script>

<style>
  @font-face {
    font-family: Gilroy;
    src: url(../static/fonts/font/Gilroy-Regular.woff2);
  }

  html,
  body {
    background-color: #ffffff;
  }

  .v-main .container {
    margin: 0 auto !important;
  }

  .v-toolbar {
    display: flex;
    justify-content: center;
    box-shadow: none !important;
    background-color: #ffffff !important;
  }

  .app-header .v-toolbar__content {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    padding: 8px 16px;
    width: 100% ;
    max-width: 100% ;
    height: 100% !important;
  }

  .nuxt-link-active .v-list-item__icon i,
  .nuxt-link-active .v-list-item__content .v-list-item__title {
    transition: all 0.2s;
    color: #2256F6 !important;
  }

  @media screen and (max-width: 900px) {
    .v-toolbar,
    .v-toolbar__content {
      padding: 8px 16px;
      width: 100vw;
      max-width: 100vw;
    }

    body {
      overflow: hidden;
    }
  }
</style>

<style lang="scss">
  .app,
  .v-application {
    font-family: "Gilroy", sans-serif;
    font-style: normal;
    color: #272727;
  }

  .content-container {
    width: 100%;
    max-width: 1470px;
    margin: 0 auto;
    padding: 0 15px;
  }

  .search-field > .v-input__control > .v-input__slot {
    box-shadow: none !important;
    background: #F6F6F6 !important;
    border-right: 2px solid #2256F6;
    border-radius: 10px;
    max-width: 40%;
    margin-left: 115px;
  }

  .nav-heading {
    font-weight: 500;
    font-size: 18px;
    line-height: 22px;
    color: #7A7A7A;
  }

  .nav-item div.v-list-item__content div.v-list-item__title {
    font-weight: 600;
    font-size: 18px;
    line-height: 22px;
    color: #000000;
  }

  .nav-item div.v-list-item__icon {
    margin-right: 15px !important;
  }

  .nav-item div.v-list-item__icon i {
    color: #272727;
  }

	.v-navigation-drawer__content {
		background-color: #f6f6f6;
		padding-top: 36px;
		padding-right: 20px;
	}

	.close-sidebar.v-icon.v-icon {
		margin-left: auto;
		margin-right: 16px;
		display: flex;
		align-items: center;
		justify-content: center;
		text-align: center;
		width: 46px;
		height: 46px;
		border-radius: 12px;
		background-color: #fff;
		overflow: hidden;
	}

	.v-list-item__avatar {
		margin: 0;
	}

	.v-list-item__title,
	.v-list-item__subtitle {
		font-size: 18px;
		line-height: 1.25;
	}

	.v-virtual-scroll__item {
		padding-bottom: 20px;

		&:last-child {
			padding-bottom: 0
		}
	}

	.v-application--is-ltr .v-list-item__avatar:first-child {
		margin-right: 27px;
	}

  .music-player {

    .main-icon {
      font-size: 30px;
      line-height: 37px;
      color: #272727;
      margin-right: 15px;
    }
    
    h2 {
      font-style: normal;
      font-weight: 600;
      font-size: 30px;
      line-height: 37px;
      color: #272727;
    }
  }
</style>
