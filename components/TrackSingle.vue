<template>
  <div style="width: 100%">
    <div
      v-if="loading"
      class="content-container"
    >
      <div
        class="d-flex justify-center align-center pa-5"
        style="min-height: 80px;"
      >
        <v-progress-circular
          indeterminate
          color="#2256F6"
        />
      </div>
    </div>

    <section
			v-if="!loading"
      class="track"
      :class="{ 'full-width': wide, 'flat': wide, 'padless': history }"
    >
      <div class="player">
				<div class="player__img">
					<v-img
						min-height="165"
						min-width="165"
						max-height="165"
						max-width="165"
						:src="track.avatar"
						style="background: #D9D9D9; border-radius: 5px;"
					/>
				</div>
				<div class="player__container d-flex flex-wrap">
					<div class="player__disliked">
						<v-icon
							color="#2256F6"
							@click="track.liked = false"
						>
							mdi-heart-off-outline
						</v-icon>
					</div>
					<div class="player__info">
						<div class="player__title">{{ track.title }}</div>
						<div class="player__artist">{{ track.artist }}</div>
					</div>
					<div class="player__liked">
						<v-icon
							color="#2256F6"
							@click="track.liked = true"
						>
							mdi-heart
						</v-icon>
					</div>
					<div class="player__progress">
						<div class="player__progress-line">
							<div 
								class="player__progress-width"
								:style="{width: progress + '%'}"
							></div>
							<div 
								class="player__handler"
								:style="{left: progress + '%'}"
								@drag="drag"
							></div>
						</div>
						<div class="player__time player__time_current">
							{{ elapsedTime ? elapsedTime : '00:00' }}
						</div>
						<div class="player__time player__time_duration">
							{{ duration }}
						</div>
					</div>
					<div class="player__controls">
						<div class="player__shuffle">
							<v-icon
								color="#2256F6"
								@click="shuffle = !shuffle"
							>
								mdi-shuffle-variant
							</v-icon>
						</div>
						<div class="player__controls-main">
							<v-icon
								class="player__arrow"
								color="#2256F6"
								@click="back(elapsed - 5)"
							>
								mdi-chevron-double-left
							</v-icon>
							<v-icon
								v-if="!playing"
								class="player__play"
								color="#fff"
								@click="play"
							>
								{{ playIcon }}
							</v-icon>
							<v-icon
								v-else
								class="player__play"
								color="#fff"
								@click="pause"
							>
								{{ pauseIcon }}
							</v-icon>
							<v-icon
								class="player__arrow"
								color="#2256F6"
								@click="forward(elapsed + 5)"
							>
								mdi-chevron-double-right
							</v-icon>
						</div>
						<div class="player__repeat">
							<v-icon
								color="#2256F6"
								:class="repeated ? 'is-active' : ''"
								@click="repeated = !repeated"
							>
								mdi-repeat
							</v-icon>
						</div>
					</div>
				</div>
			</div>
    </section>
  </div>
</template>

<script>
  import { Howl } from 'howler'

  const formatTime = second => new Date(second * 1000).toISOString().substr(14, 5);

  export default {
    name: 'TrackSingle',
    props: {
      wide: {
        type: Boolean,
        default: false
      },
      flat: {
        type: Boolean,
        default: false
      },
      tracks: {
        type: Array,
        required: true
      },
			currentSound: {
        type: Number,
        required: true
      },
      history: {
        type: Boolean,
        default: false
      },
      autoplay: {
        type: Boolean,
        default: false
      },
      color: {
        type: String,
        default: '#2256F6'
      },
      playIcon: {
        type: String,
        default: 'mdi-play'
      },
      pauseIcon: {
        type: String,
        default: 'mdi-pause'
      }
    },
    data () {
      return {
				track: {
					artist: '',
					title: '',
					avatar: '',
					plays: '',
					duration: ''
				},
				trackLength: 0,
        loading: true,
        playing: false,
        paused: false,
        sound: null,
				repeated: false,
        elapsed: 0,
        totalDuration: 0,
        playerVolume: 1,
        updateTimeIntervalId: null
      }
    },
    computed: {
      duration: function () {
        return this.sound._duration ? formatTime(this.sound._duration) : ''
      },
      elapsedTime: function () {
        return this.elapsed ? formatTime(this.elapsed) : ''
      },
      source () {
        return this.track.source;
      },
      title () {
        return this.track.title;
      },
      artist () {
        return this.track.artist;
      },
      progress () {
        return parseInt((this.elapsed / this.sound._duration) * 100)
      }
    },
    mounted () {
			this.init();
    },
    methods: {
			back(time) {
				time = time <= 0 ? 0 : time;
				this.sound.seek(time);
				this.elapsed = time;
			},
			forward(time) {
				time = time >= this.sound._duration ? this.sound._duration : time;
				this.sound.seek(time);
				this.elapsed = time;
			},
			drag(event) {
				let time = 0, pos1 = 0, pos2 = 0, progress = 0, progressPosition = 0, progressLineWidth = event.target.parentElement.offsetWidth;
				event.target.onmousedown = dragMouseDown;

				function dragMouseDown(e) {
					console.log(e);
					e = e || window.event;
					e.preventDefault();
					pos2 = e.clientX;
					document.onmouseup = closeDragElement;
					document.onmousemove = elementDrag;
				}

				let elementDrag = (e) => {
					e = e || window.event;
					e.preventDefault();
					let start = event.target.parentElement.getBoundingClientRect().x;
					let end = event.target.parentElement.getBoundingClientRect().x + progressLineWidth;
					pos1 = pos2 - start;
					pos2 = e.clientX;
					if(pos2 <= start) {
						progressPosition = 0;
					} else if (pos2 >= end) {
						progressPosition = (progressLineWidth) + "px";
					} else {
						progressPosition = pos1 + "px";
					}
					progress = (parseInt(progressPosition) / progressLineWidth) * 100;
					time = this.sound._duration * (progress / 100);
					this.sound.seek(time);
					this.elapsed = time;
				}

				function closeDragElement() {
					// stop moving when mouse button is released:
					document.onmouseup = null;
					document.onmousemove = null;
				}
			},
      play () {
        if (!this.sound) return;

        this.sound.play();

        this.paused = false;
        this.playing = true;
      },
      pause () {
        if (!this.sound) return;

        this.sound.pause()

        this.paused = true;
        this.playing = false;
      },
			stop () {
        if (!this.sound) return;

        this.sound.stop();
				this.autoplay = false;
				this.init();

        this.paused = true;
        this.playing = false;
      },
			createSound(track) {
				this.track = track;
				this.loading = true;

				this.elapsed = 0;
        this.totalDuration = 0;

				clearInterval(this.updateTimeIntervalId);

				if (this.sound) {
					this.sound.unload();
          this.sound = null;
        }

				this.sound = new Howl({
          src: track.source,
          html5: true,
					onend: () => {
						if (this.playing && this.repeated) {
							this.elapsed = 0;
							this.sound.seek(0);
						} else if (this.playing && !this.repeated) {
							this.currentSound = (this.currentSound === this.trackLength - 1) ? 0 : this.currentSound + 1;
							this.createSound(this.tracks[this.currentSound]);
						}
      	  }
        });

				this.totalDuration = track.duration;
        this.elapsed = this.elapsed;

        this.updateTimeIntervalId = setInterval(this.updateClock, 1000);

				this.sound.once('load', () => {
					this.loading = false;
					if (this.autoplay) {
						this.play();
					}
				});
			},
      init: function () {
				this.trackLength = this.tracks.length;
				this.createSound(this.tracks[this.currentSound]);
      },
      updateClock () {
				if(this.playing) {
					this.elapsed++;
				}
      }
    }
  }
</script>

<style lang="scss" scoped>
  $animation-name: 'pump-it-up';

  @mixin keyframes($animationName) {
    @-webkit-keyframes #{$animationName} {
      @content;
    }
    @-moz-keyframes #{$animationName} {
      @content;
    }
    @-o-keyframes #{$animationName} {
      @content;
    }
    @keyframes #{$animationName} {
      @content;
    }
  }

  @for $i from 1 through 9 {
    @include keyframes(#{$animation-name+$i}) {
      20% {

      }
      40% {
        height: random(50) + px;
      }
      60% {
        height: random(50) + px;
      }
      80% {
        height: random(50) + px;
      }
      100% {
        height: random(50) + px;
      }
    }
  }

	.player {
		padding: 25px 30px 37px;
		background: #FFFFFF;
		border-radius: 20px;

		&__img {
			display: flex;
			justify-content: center;
			margin-bottom: 10px;
		}

		&__info {
			flex: 1;
			text-align: center;
			padding: 0 15px;
		}

		&__title {
			font-size: 18px;
			line-height: 1.25;
			font-weight: 700;
			margin-bottom: 7px;
		}

		&__artist {
			font-size: 18px;
			line-height: 1.25;
			font-weight: 300;
		}

		&__progress {
			margin-top: 40px;
			flex: 1 100%;
			display: flex;
			flex-wrap: wrap;
			justify-content: space-between;

			&-line {
				flex: 1 100%;
				position: relative;
				height: 1px;
				background: #000000;
				margin: 0 15px 8px;
			}

			&-width {
				position: absolute;
				left: 0;
				top: 50%;
				transform: translate(0,-50%);
				height: 3px;
				background: #2256F6;
			}
		}

		&__handler {
			position: absolute;
			cursor: grab;
			top: 50%;
			transform: translate(0,-50%);
			width: 9px;
			height: 9px;
			border-radius: 50%;
			background: #2256F6;
		}

		&__time {
			font-size: 14px;
			font-weight: 300;
		}

		&__controls {
			display: flex;
			align-items: center;
			justify-content: center;
			flex: 1 100%;
			max-width: 230px;
			margin: 0 auto;

			&-main {
				margin: 0 27px;
				display: flex;
				align-items: center;
			}
		}

		&__shuffle {
			font-size: 22px;
			line-height: 1;
		}
		
		&__repeat {
			font-size: 22px;
			line-height: 1;

			button:focus::after {
				opacity: 0;
			}
			button.is-active::after {
				opacity: 0.12;
			}
		}

		&__arrow {
			font-size: 21px;
			line-height: 1;
		}

		&__play {
			width: 40px;
			height: 40px;
			border-radius: 50%;
			background-color: #2256f6;
			display: flex;
			align-items: center;
			justify-content: center;
			margin: 0 23px;
		}
	}

  .track {
    background: #FFFFFF;
    box-shadow: 0px 4px 34px rgba(0, 0, 0, 0.18);
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    // padding: 16px;

    &.full-width {
      width: 100%;
    }

    &.flat {
      box-shadow: none;
    }

    &.padless {
      padding-top: 0;
      padding-bottom: 0;
    }

    h3 {
      font-weight: 600;
      font-size: 18px;
      line-height: 21px;
      color: #272727;
      text-align: left;
    }

    p {
      font-weight: 400;
      font-size: 16px;
      line-height: 18px;
      color: #272727;
      text-align: left;
    }

    .params {
      flex: 1;
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      align-items: center;

      .duration {
        font-weight: 500;
        font-size: 16px;
        line-height: 19px;
        color: #272727;
      }

      .control-btn {
        
        .v-icon {
          height: 40px;
          font-size: 34px;
          width: 40px;
        }
      }
    }

    #levels {
      width: 70px;
      height: 50px;
      top: 50%;
      margin: 0 auto;
      position: relative;

      .level {
        width: 3px;
        height: 50px;
        margin-left: 1px;
        display: inline-block;
        position: relative;

        &:after {
          content: ' ';
          position: absolute;
          bottom: 0;
          left: 0;
          background: rgba(34, 87, 246, 0.6);
          width: 3px;
        }

        @for $i from 1 through 9 {
          &.level#{$i}:after {
            height: random(50) + px;
            animation: #{$animation-name+$i} 600 + random(500) + ms linear infinite alternate;
          }
        }
      }
    }
  }
</style>
