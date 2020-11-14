<template>
  <div class="container" >
    <div class="flex flex-wrap w-auto">
      <!-- SIDEBAR NAV -->
      <div class="w-1/5 p-2 ">
        <div class="text-gray-700 text-center h-48 text-xs ">
          <div class="flex flex-col bg-gray-100" v-if="date_today && genres">
            <!-- DATEPICKER -->
            <client-only 
              ><date-picker
                :inline="true"
                placeholder="MM/DD/YYYY"
                format="yyyy/MM/dd"
                v-model="date_today"
                @selected="selectedDate()"
                calendar-class="calendar-container"
                :disabled-dates="state.disabled_dates"
                :full-month-name="true"
            /></client-only>
            <!-- END DATEPICKER -->

            <!-- GENRES LIST -->
            <div
              class="text-gray-800 text-center font-bold"
              v-for="(item, index) in genres"
              :key="index"
            >
              <div
                @click="onGenreSelected(item)"
                :id="item.genreName"
                :class="{
                  'py-2': true,
                  'cursor-pointer': true,
                  'hover:bg-gray-300': true,
                  'sidebar-button': isGenreSelected(item),
                  'bg-gray-300': isGenreSelected(item),
                }"
              >
                {{ item.genreName }}
              </div>
            </div>
            <!-- END GENRES LIST -->
          </div>
        </div>
      </div>
      <!-- END SIDEBAR NAV -->

      <!-- CONTENT -->
      <div
        class="w-3/4 p-2 mt-2 bg-gray-100"
        v-if="releases && releases.length > 1"
        v-cloak
      >
        <!-- CONTENT HEADER -->
        <div class="flex text-gray-700 text-center justify-between">
          <div class="text-xs align-text-bottom align-baseline p-0 m-0">
            <strong
              >{{ releases.length }} releases ({{ tracks.length }} tracks)
            </strong>
            found on your search criteria
          </div>
          <!-- <v-select
            class="style-chooser"
            dir="rtl"
            append-to-body
            :clearable="false"
            :searchable="false"
            :options="searchBy.options"
            v-model="searchBy.selected"
          ></v-select> -->
        </div>
        <!-- END CONTENT HEADER -->

        <!-- RELEASES -->
        <div
          v-for="(release, i) in releases"
          :key="i"
          class="flex flex-row py-2"
        >
          <!-- COVER -->
          <div class="flex flex-col">
            <img class="w-56" :src="'https://localhost:9001/api/v1/Image/' + release.cover" />
          </div>
          <!-- END COVER -->

          <!-- DATE -->
          <div
            class="flex flex-col text-gray-700 text-center ml-2 mr-1 w-16 h-16 bg-gray-200"
          >
            <div class="pt-4 font-semibold antialiased text-xl">
              {{ $moment(release.created).format('DD') }}
            </div>
            <div class="pt-0 antialiased text-sm">{{ $moment(release.created).format('MMM') }}</div>
          </div>
          <!-- END DATE -->

          <!-- TRACKLIST -->
          <div class="flex-grow flex-col">
            <div class="flex-row bg-gray-200 p-2 text-md">
              <span class="font-semibold">{{ release.title }}</span>
              <span class="float-right"
                ><a href="#" class="hover:underline text-sm font-semibold">{{
                  release.label
                }}</a
                >&nbsp;
                <a
                  class="ms-release-dwnldbtn float-right hover:bg-gray-100 font-semibold lowercase"
                  :href="'https://localhost:9001/api/v1/Download/'+release.fileId"
                  >&nbsp;&nbsp;&nbsp;ZIP&nbsp;&nbsp;&nbsp;</a
                ></span
              >
            </div>
            <div v-for="(track, index) in release.tracks" :key="index">
              <div
                class="flex-row text-gray-700 text-left hover:bg-gray-300 bg-gray-200 cursor-pointer px-2 track-title-container place-items-center pt-1 text-sm"
                :id="release.id.toString() + track.id.toString()"
                @click="play(track)"
              >
                <div class="flex-col inline-block text-base tracking-wide">
                  <p class="truncate">{{ track.artist }} - {{ track.title }}</p>
                </div>
                <div class="flex-col inline float-right lowercase text-base">
                  {{ track.genreName }}
                  <a :href="'https://localhost:9001/api/v1/Download/'+track.fileId"
                    class="ms-release-dwnldbtn hover:bg-gray-100 z-10 font-bold"
                    >&nbsp;&nbsp;mp3&nbsp;&nbsp;</a
                  >
                </div>
              </div>

              <div
                style="display: none"
                :id="track.id.toString()"
                class="flex-1 text-gray-700 text-center bg-gray-300 content-center py-1 h-14"
              ></div>
            </div>
          </div>
          <!-- END TRACKLIST -->
        </div>
        <!-- END RELEASES -->

        <audio
          v-if="show_audio"
          ref="audio"
          :src="'https://localhost:9001/api/v1/Preview/'+current.previewId"
          class="audio-element"
        ></audio>
        <av-waveform
          v-if="waveform"
          :key="current.id"
          :audio-controls="false"
          :ref-link="'audio'"
          :canv-width="634"
          :canv-height="40"
          :played-line-color="'black'"
          :playtime-with-ms="false"
          :playtime-font-family="'arial'"
          :playtime-font-color="'black'"
          :playtime-font-size="12"
          :playtime-text-bottom="true"
          :playtime-slider-color="'black'"
          :noplayed-line-color="'gray'"
          :playtime-slider-width="0.8"
          :cors-anonym="true"
        ></av-waveform>
      </div>

      <div
        class="w-3/4 p-2 mt-2 bg-gray-100 text-center py-32 text-gray-700"
        v-else
        v-cloak
      >
        <p class="font-bold">No tracks found with your selection</p>
        <span class="smiley">:-(</span>
      </div>
      <!-- END CONTENT -->
      <!-- PAGINATOR -->
      <div class="w-1/5 p-2"></div>
      <div class="w-3/4 mt-3 text-center text-gray-700 mb-16" v-cloak>
        <div class="text-gray-700 flex justify-between mt-auto">
          <button
            @click="goPrevious"
            class="btn btn-nav bg-gray-100 text-center text-xs uppercase font-bold"
          >
            previous date
          </button>
          <button
            @click="goNext"
            class="btn btn-nav bg-gray-100 text-center text-xs uppercase font-bold"
          >
            next date
          </button>
        </div>
        <!-- END PAGINATOR -->
      </div>
    </div>
  </div>
</template>

<script>
import Axios from 'axios'

export default {
  data() {
    return {
      isActive: false,
      lastElement: null,
      lastPlayer: null,
      currentId: null,
      currentIndex: null,
      currentRelease: null,
      players: [],
      titles: [],
      date_selected: '',
      player: null,
      element: null,
      current: null,
      waveform: false,
      show_audio: false,
      canvas: null,
      max: null,
      isPlaying: false,
      date_today: new Date(),
      searchBy: {
        options: [
          { label: 'BY POPULARITY', value: 1 },
          { label: 'BY DATE', value: 2 },
        ],
        selected: 'BY POPULARITY',
      },
      state: {
        disabled_dates: {
          from: new Date(),
        },
      },
      genres: [
      ],
      genres_selected: [2, 7, 5],
      tracks: [],
      releases: [],
      all_releases: [],
    }
  },
  methods: {
    async init() {
      await this.getGenres()
      await this.getAllReleases().then(() => {
        this.genres = this.getGenres()
        if (this.all_releases) {
          this.date_today = this.$moment(this.all_releases[0].date).toDate()
          this.releases = this.all_releases;
          this.releases = this.removeDuplicateReleases(this.releases)
          this.state.disabled_dates.from = this.$moment(this.getLastDate()).toDate()
          this.$nextTick(() => {
            this.tracks = this.getTracks(this.releases)
            this.max = this.tracks.length - 1
          })
        }
      })
    },
    async download(id)
    {
      const ar = await Axios.get('https://localhost:9001/api/v1/Download/'+id)
    },
    async getAllReleases() {
      const ar = await Axios.get('https://localhost:9001/api/v1/Album').then(
        (res) => {
          this.all_releases = res.data.data
        }
      )
    },

    play(track) {
      this.removeCanvas()
      if (track !== null && track !== undefined) {
        this.current = track
        this.show_audio = true
        this.waveform = true
        this.$nextTick(() => {
          this.currentIndex = this.getCurrentTrackIndex(track)
          this.currentId = track.id
          this.currentRelease = this.getCurrentRelease(track)
          var element = document.getElementById(track.id.toString())
          this.element = element
          var canvas = this.getCanvas()
          var player = document.getElementsByClassName('audio-element')[0]
          this.player = player
          this.setTitleClasses(track)
          player.load()
          if (element.style.display === 'block') {
            if (this.lastElement) {
              this.lastElement.style.display = 'none'
            }
            element.style.display = 'none'
            this.removeTitleClass(track)
            this.waveform = false
            this.show_audio = false
            this.current = null
          } else {
            if (this.lastElement) {
              this.lastElement.style.display = 'none'
            }
            element.style.display = 'block'
            element.append(canvas)
            player.play()
          }
          this.lastElement = element
          player.addEventListener('ended', this.handleEnded)
        })
      }
    },
    handleEnded() {
      if (this.currentIndex < this.max) {
        var next = this.tracks[this.currentIndex + 1]
        this.currentIndex = this.currentIndex + 1
        this.play(next)
      }
    },
    setTitleClasses(track) {
      this.removeAllTitleClasses()
      var lastTitle = document.getElementById(
        this.currentRelease.id.toString() + track.id.toString()
      )
      var currentTitle = document.getElementById(
        this.currentRelease.id.toString() + track.id.toString()
      )
      if (currentTitle) {
        currentTitle.classList.remove('bg-gray-200')
        currentTitle.classList.add('bg-gray-300')
      }
    },
    removeAllTitleClasses() {
      var titles = document.getElementsByClassName('track-title-container')
      titles.forEach((element) => {
        element.classList.remove('bg-gray-300')
        element.classList.add('bg-gray-200')
      })
    },
    removeTitleClass(track) {
      var currentTitle = document.getElementById(
        this.currentRelease.id.toString() + track.id.toString()
      )
      if (currentTitle) {
        currentTitle.classList.remove('bg-gray-300')
        currentTitle.classList.add('bg-gray-200')
      }
    },
    fillTrackList() {
      this.tracks = []
      this.releases.forEach((element) => {
        element.tracks.forEach((child) => {
          this.tracks.push(child)
        })
      })
    },
    getCurrentRelease(track) {
      var result
      this.releases.forEach((element) => {
        element.tracks.forEach((child) => {
          if (child === track) {
            result = element
          }
        })
      })
      return result
    },
    getCurrentTrackIndex(track) {
      return this.tracks.indexOf(track)
    },
    selectedDate() {
      this.$nextTick(() => {
        this.releases = this.getReleases(
          this.$moment(this.date_today).format('YYYY-MM-DD')
        )
        // this.releases = this.getReleasesMatching(this.genres_selected)
        this.releases = this.removeDuplicateReleases(this.releases)
        this.tracks = this.getTracks(this.releases)
        this.waveform = false
        this.show_audio = false
        this.current = null
        // this.setGenreLabels()
        this.max = this.tracks.length - 1
        this.removeAllTitleClasses()
        if (this.player) {
          this.player.load()
        }
        if (this.element) {
          this.element.style.display = 'none'
        }
      })
    },
    goPrevious() {
      this.date_today = this.$moment(this.date_today).subtract(1, 'd').format()
      this.selectedDate()
    },
    goNext() {
      if (this.date_today !== this.getLastDate()) {
        this.date_today = this.$moment(this.date_today).add(1, 'd').format()
        this.selectedDate()
      }
    },
    isGenreSelected(genre) {
      if (genre) {
        return this.genres_selected.includes(genre.id)
      } else {
        return false
      }
    },
    setTitleElementClass(element) {
      if (element) {
        if (element.style.display == 'none') {
          element.style.display = 'block'
        } else {
          element.style.display = 'none'
        }
      }
    },
    getTitleElementClass(track) {
      if (document && track) {
        return document.getElementById('title-' + track.id.toString())
      }
    },
    getCanvas() {
      if (document) {
        return document.getElementsByTagName('canvas')[0]
      }
    },
    removeCanvas() {
      var canvas_list = document.getElementsByTagName('canvas')
      if (canvas_list) {
        this.waveform = false
        this.show_audio = false
        this.$nextTick(() => {
          canvas_list.forEach((element) => {
            element.remove()
          })
        })
      }
    },
    getLastDate() {
      return this.all_releases[0].date
    },
    getReleases(date) {
      if (date) {
        return this.all_releases.filter((x) => x.date === date)
      }
    },
    getTracks(releases) {
      var tracks = []
      if (releases) {
        releases.forEach((element) => {
          element.tracks.forEach((child) => {
            tracks.push(child)
          })
        })
      }
      return tracks
    },
    async getGenres() {
      const ar = await Axios.get('https://localhost:9001/api/v1/Genre?PageSize=30').then(
        (res) => {
          this.genres = res.data.data
        }
      )
    },
    onGenreSelected(genre) {
      if (this.genres_selected.includes(1)) {
        var index = this.genres_selected.indexOf(1)
        if (index > -1) {
          this.genres_selected.splice(index, 1)
        }
      }
      if (!this.genres_selected.includes(genre.id)) {
        this.genres_selected.push(genre.id)
      } else {
        var index = this.genres_selected.indexOf(genre.id)
        if (index > -1) {
          this.genres_selected.splice(index, 1)
        }
      }
      if (genre.id === 1 || this.genres_selected.length < 1) {
        this.genres_selected = [1]
      }
      this.$nextTick(() => {
        this.releases = this.getReleases(
          this.$moment(this.date_today).format('YYYY-MM-DD')
        )
        this.waveform = false
        // this.releases = this.getReleasesMatching(this.genres_selected)
        this.releases = this.removeDuplicateReleases(this.releases)
        this.tracks = this.getTracks(this.releases)
        // this.setGenreLabels()
        this.max = this.tracks.length - 1
        this.removeAllTitleClasses()
        if (this.player) {
          this.player.load()
        }
        if (this.element) {
          this.element.style.display = 'none'
        }
      })
    },
    getReleasesMatching(genre) {
      var result = []
      this.releases = []
      var matching = this.getReleases(
        this.$moment(this.date_today).format('YYYY-MM-DD')
      )
      if (genre[0] === 1) {
        return this.getReleases(
          this.$moment(this.date_today).format('YYYY-MM-DD')
        )
      } else {
        genre.forEach((g) => {
          if (!matching.some((x) => x === result)) {
            let exists = false
            matching.forEach((r) => {
              exists = r.tracks.some(function (e) {
                return e.genre === g
              })
              if (exists) {
                result.push(r)
                return true
              }
            })
          }
        })
        return result
      }
    },
    getGenreLabel(genre) {
      var result = this.genres.find((x) => x.id === genre)
      return result.genreName
    },
    setGenreLabels() {
      this.tracks.forEach((t) => {
        t.genre_label = this.getGenreLabel(t.genre)
      })
    },
    removeDuplicateReleases(releases) {
      return releases.filter(
        (v, i, a) => a.findIndex((t) => t.id === v.id) === i
      )
    },
  },
  created() {
    this.init()
  },
  mounted() {},
  computed: {
    isNext() {
      var date = this.all_releases[0].date
      return this.date_today === date
    },
    isPrevious() {
      var date = this.all_releases[0].date
      return this.date_today <= date
    },
  },
}
</script>

<style>
.container {
  margin: 0 auto;
  display: flex;
}

a.ms-release-dwnldbtn {
  display: inline;
  border: 1px solid #9faeb4;
  color: #45666f;
  text-shadow: none;
  -webkit-flex-shrink: 0;
  -ms-flex-shrink: 0;
  flex-shrink: 0;
  -webkit-flex-wrap: 0;
  -ms-flex-wrap: 0;
  flex-wrap: 0;
  text-align: center;
  padding: 0;
  line-height: 13px;
  font-size: 11px;
  font-family: 'segoeuib', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  text-transform: uppercase;
  text-decoration: none !important;
  margin: 1px 0 1px 0;
  -webkit-box-sizing: content-box;
  -moz-box-sizing: content-box;
  box-sizing: content-box;
}

.btn-nav {
  padding: 10px;
  line-height: 11px;
  font-family: 'segoeuib', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  color: #3d464d;
  width: auto;
  display: flex;
  text-decoration: none;
  border: none;
}

.calendar-container {
  display: block;
  width: auto !important;
  background: transparent !important;
  border: none !important;
  font-family: 'segoeuib', 'Helvetica Neue', Helvetica, Arial, sans-serif !important;
}
.calendar-container .cell.selected {
  background: #cbd5e0 !important;
}

.calendar-container .cell:hover {
  border-color: #cbd5e0 !important;
}

canvas {
  padding-left: 0;
  padding-right: 0;
  margin-left: 6px;
  margin-right: auto;
  display: flex;
  align-content: flex-start;
  cursor: pointer;
}

.style-chooser .vs__search::placeholder,
.style-chooser .vs__dropdown-toggle,
.style-chooser .vs__dropdown-menu {
  border-radius: 0px;
  border: none;
  border-bottom: 2px solid #9faeb4;
  text-transform: uppercase;
  font-variant: small-caps;
  font-family: 'segoewpb', 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

.vs__fade-enter-active,
.vs__fade-leave-active {
  transition: none;
}
.style-chooser .vs__clear,
.style-chooser .vs__open-indicator {
  fill: #39406600;
}
.vs__actions {
  display: none;
}
.cell {
  height: 25px !important;
  line-height: 25px !important;
}

.vdp-datepicker__calendar .cell.day-header {
  font-size: 79% !important;
  white-space: nowrap;
  cursor: inherit;
  text-transform: uppercase;
  font-weight: bold;
  border-bottom: 1px solid #ebe8e8;
}

.vdp-datepicker__calendar .cell.day-header:hover {
  border: none !important;
  font-size: 79% !important;
  white-space: nowrap !important;
  cursor: inherit !important;
  text-transform: uppercase !important;
  font-weight: bold !important;
  margin: auto;
}

.vdp-datepicker__calendar .disabled {
  color: transparent;
  cursor: default;
}

.smiley {
  display: block;
  font-size: 100px;
  font-family: Arial;
  width: 130px;
  height: 130px;
  line-height: 115px;
  margin: auto;
  -webkit-transform: rotate(90deg);
  -ms-transform: rotate(90deg);
  -o-transform: rotate(90deg);
  transform: rotate(90deg);
}

.day__month_btn.up {
  -webkit-font-smoothing: antialiased;
  font-size: 0.97rem;
  --text-opacity: 1;
  color: rgba(74, 85, 104, var(--text-opacity));
  --bg-opacity: 1;
  font-family: 'segoeuib', 'Helvetica Neue', Helvetica, Arial, sans-serif !important;
  line-height: 40px;
  border-width: 0;
  border-style: solid;
  border-color: #e2e8f0;
  margin: 0;
  box-sizing: border-box;
  display: inline-block;
  text-align: center;
  width: 71.42857142857143%;
  float: left;
  cursor: pointer;
}

.vdp-datepicker__calendar header .prev,
.vdp-datepicker__calendar header .next {
  width: 14.285714285714286%;
  float: left;
  text-indent: -10000px;
  position: relative;
  font-size: 9px !important;
}

.btn {
  display: inline-block;
  margin-bottom: 0;
  font-weight: normal;
  text-align: center;
  vertical-align: middle;
  touch-action: manipulation;
  cursor: pointer;
  background-image: none;
  border: 1px solid transparent;
  white-space: nowrap;
  padding: 4px 8px;
  font-size: 14px;
  line-height: 1.42857143;
  border-radius: 0;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

input[type='button'] {
  width: 120px;
  height: 60px;
  margin-left: 35px;
  display: block;
  background-color: gray;
  color: white;
  border: none;
  outline: none;
}
</style>