<template>
  <div id="app-stage" @keydown.esc="handleEsc">
    <MobileBanner class="mobile-stage" />
    <header>
        <WELogo 
          class="logo-display"
          :small="true"
          :searching="searching" 
        />
        <h1 v-if="searching" class="searching">Searching the Skies...</h1>
        <h1 v-if="!searching">
          NeoWs watches 
          <span class="count">
            <ICountUp
              :delay="delay"
              :endVal="bodyCount"
            />
          </span> 
          object<span v-if="bodyCount > 1 || bodyCount < 1">s </span> 
          <span v-if="todaySearchIsActive" class="today-indicator">today</span>
          <span v-if="!todaySearchIsActive"> on </span>
          <span v-if="!todaySearchIsActive" class="on-date">{{ dateToSearch }}</span>
        </h1>
    </header>
    
    <main>
      <section class="threat-stage" :class="{ 'active-threats': threatCount > 0, 'inviz': searching }">
        <p><strong>{{ threatCount }}</strong><small> threat<span v-if="threatCount > 1 || threatCount < 1">s</span> detected</small></p>
      </section>

      <BodyDashboard 
        :bodyCollection="foundBodies" 
        :bodyCount="bodyCount"
        class="body-dashboard-wrapper"
      />

      <NoBodiesFound v-if="hasSearched && bodyCount < 1" />
    </main>
    <Earth />

    <section class="date-editor">
      <small>Explore Other Dates Using Your Up and Down Arrows or by Typing: </small>
      
      <label for="year">Year</label>
      <input 
        v-model.lazy="userStartYear"
        v-debounce="delay"
        ref="userStartYear"
        type="number"
        id="year" 
        name="year"
        min="1900" 
        max="2600"
      />

      <label for="month">Month</label>
      <input 
        v-model.lazy="userStartMonth" 
        v-debounce="delay"
        ref="userStartMonth"
        type="number"
        id="month" 
        name="month"
        min="01" 
        max="12"
        maxlength="2"
      />

      <label for="day">Day</label>
      <input 
        v-model.lazy="userStartDay"
        v-debounce="delay"
        ref="userStartDay"
        type="number"
        id="day" 
        name="day"
        min="01" 
        max="31"
        maxlength="2"
      />
        
      <span v-if="!todaySearchIsActive" class="back-to-today" @click="setSearchToToday">Back to Today</span>
        
      <section class="about">
        <ul>
          <li><a href="https://github.com/drinkingtheink/neows-watch" target="_blank" alt="About this Project">About this Project</a></li>
          <li><a href="http://drinkingtheink.com/" target="_blank" alt="About the Author">About the Author</a></li>
        </ul>
      </section>
    </section>
      
    <Modal
      v-show="modalIsVisible"
      @close="closeModal"
      :content="modalContent"
      class="modal-wrapper"
    />

    <SpaceBG 
      class="space-bg-stage" 
      :style="{ 'top': `${spaceBGTop}px`, 'left': `${spaceBGLeft}px` }"
    />
  </div>
</template>

<script>
import WELogo from './WELogo.vue';
import BodyDashboard from './BodyDashboard.vue';
import NoBodiesFound from './NoBodiesFound.vue';
import MobileBanner from './MobileBanner.vue';
import Modal from './Modal.vue';
import Earth from './Earth.vue';
import SpaceBG from './SpaceBG.vue';
import { EventBus } from '../EventBus';
import ICountUp from 'vue-countup-v2';
import debounce from 'v-debounce';

export default {
  name: 'AppStage',
  components: {
    WELogo,
    BodyDashboard,
    NoBodiesFound,
    MobileBanner,
    Modal,
    Earth,
    SpaceBG,
    ICountUp,
  },
  data() {
    return {
      apiUrl: 'https://api.nasa.gov/neo/rest/v1/feed?',
      apiKey: 'Z1Y1LoyLfSDcQJI51fl8E1tYsrZQgnvU09C5pV36',
      searching: false,
      searchError: false,
      hasSearched: false,
      bodyCount: 0,
      foundBodies: [],
      modalIsVisible: false,
      modalContent: null,
      userStartYear: null,
      userStartMonth: null,
      userStartDay: null,
      options: {
        useEasing: true,
        useGrouping: true,
        separator: ',',
        decimal: '.',
        prefix: '',
        suffix: ''
      },
      spaceBGTop: 0,
      spaceBGLeft: 0,
      delay: 1000,
    }
  },
  computed: {
    today() {
      return new Date();
    },
    currentDay() {
      return String(this.today.getDate()).padStart(2, '0');
    },
    currentMonth() {
      return String(this.today.getMonth() + 1).padStart(2, '0');
    },
    currentYear() {
      return this.today.getFullYear();
    },
    searchToday() {
      return `${this.currentYear}-${this.currentMonth}-${this.currentDay}`;
    },
    dateToSearch() {
      return this.newDateReady ? `${this.userStartYear}-${this.userStartMonth}-${this.userStartDay}` : this.searchToday;
    },
    dataUrl() {
      return `${this.apiUrl}start_date=${this.dateToSearch}&end_date=${this.dateToSearch}&api_key=${this.apiKey}`
    },
    newDateReady() {
      return this.userStartYear && this.userStartMonth && this.userStartDay;
    },
    todaySearchIsActive() {
      return this.searchToday === this.dateToSearch;
    },
    threatCount() {
      let threats = [];

      this.foundBodies.forEach((body) => {
        if (body.is_potentially_hazardous_asteroid) {
          threats.push({ body })
        }
      })

      return threats.length;
    },
    queryStrings() {
      return window.location.search;
    },
  },
  watch: {
    newDateReady () {
      this.getData();
    },
    userStartYear () {
      if (this.newDateReady) this.getData();

      if (this.userStartYear.toString().length > 4) {
        let concatYr = this.userStartYear.toString().substring(0,4);
        this.userStartYear = parseInt(concatYr);
      }

      if (this.userStartYear === '') {
        this.userStartYear = this.currentYear;
      }
    },
    userStartMonth () {
      if (this.newDateReady) this.getData();

      if (this.userStartMonth.toString().length > 2) {
        let concatMm = this.userStartMonth.toString().substring(0,2);
        this.userStartMonth = parseInt(concatMm);
      }

      if (this.userStartMonth === '') {
        this.userStartMonth = 1;
      }
    },
    userStartDay () {
      if (this.newDateReady) this.getData();

      if (this.userStartDay.toString().length > 2) {
        let concatDd = this.userStartDay.toString().substring(0,2);
        this.userStartDay = parseInt(concatDd);
      }

      if (this.userStartDay === '') {
        this.userStartDay = 1;
      }
    },
  },
  mounted() {
    if (this.queryStrings) {
      this.handleQueryString(this.queryStrings);
    } else {
      this.setSearchToToday();
    }

    EventBus.$on('body-selected', (payload) => {
      this.modalContent = payload;
      this.showModal();
    });

    EventBus.$on('search-today', () => {
      this.setSearchToToday();
    });

    EventBus.$on('to-year-input', () => {
      this.$refs.userStartYear.focus();
    });

    this.$refs.userStartYear.focus();
  },
  methods: {
    handleEsc() {
      if (this.modalIsVisible) {
        this.modalIsVisible = false;
      }
    },
    showModal() {
      this.modalIsVisible = true;
    },
    closeModal() {
      this.modalIsVisible = false;
    },
    setSearchToToday() {
      this.userStartYear = this.currentYear;
      this.userStartMonth = this.currentMonth;
      this.userStartDay = this.currentDay;
    },
    handleQueryString(queryStrings) {
      let urlParams = new URLSearchParams(queryStrings);
      let yyyy = urlParams.get('yyyy');
      let mm = urlParams.get('mm');
      let dd = urlParams.get('dd');
      
      this.userStartYear = yyyy;
      this.userStartMonth = mm;
      this.userStartDay = dd;
    },
    getRandomInt(min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
    adjustSpaceBG() {
      this.spaceBGTop = this.getRandomInt(-5, -1040);
      this.spaceBGLeft = this.getRandomInt(-8, -700);
    },
    updateQueryStrings() {
      let urlParams = new URLSearchParams(this.queryStrings);

      urlParams.set('yyyy', this.userStartYear);
      urlParams.set('mm', this.userStartMonth);
      urlParams.set('dd', this.userStartDay);

      history.pushState(null, null, "?"+urlParams.toString());
    },
    getData() {
      this.adjustSpaceBG();
      this.searching = true;
      this.searchError = false;
      this.hasSearched = false;
      
      fetch(this.dataUrl)
        .then(response => {
          return response.json();
        })
        .then(respJSON => {
          this.bodyCount = respJSON.element_count;
          let nearEarthObjects = respJSON.near_earth_objects;
          let objectCollection = Object.values(nearEarthObjects).flat();
          objectCollection.sort((a,b) => {
            if(a.close_approach_data[0].miss_distance.miles > b.close_approach_data[0].miss_distance.miles) return 1;
            if(a.close_approach_data[0].miss_distance.miles < b.close_approach_data[0].miss_distance.miles) return -1;
            return 0;
          });
          this.foundBodies = objectCollection;
          this.updateQueryStrings();

          setTimeout(() => {
            this.searching = false;
            this.hasSearched = true;
          }, 2000);
        })
        .catch(error => {
          console.error('There has been a problem with your fetch operation:', error);
          this.searching = false;
          this.searchError = true;
        });
      }
  },
  directives: {
    debounce,
  },
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';
@import '../styles/typography';

$blackOpac: 0.7;

.date-editor {
  border-top: 2px solid $yellow;
  z-index: 4;
  background-color: rgba(black, $blackOpac);
  padding: 1rem;
  position: absolute;
  left: 0; right: 0; bottom: 0;
  margin: auto;
  display: flex;
  justify-content: center;
  align-content: center;
  color: $yellow;

  label, small {
    color: $yellow;
  }

  label {
    margin: 0 0.5rem 0 1rem;
  }

  small {
    text-transform: uppercase;
    margin-right: 1rem;
    max-width: 22rem;
  }

  input {
    color: $black;
    padding: 5px 10px;
    font-size: 110%;
    border-radius: 5px;
    outline: none;
    border: none;
    max-width: 5rem;
  }
}

header {
    background-color: rgba(black, $blackOpac);
    border-bottom: 2px solid $yellow;
    height: 6rem;

    h1 {
        font-size: 110%;
        justify-content: center;
        margin: 0;
        padding: 0;
        text-align: center;
    }

    .count {
      color: white;
      font-family: inherit;
    }

    span {
      font-family: inherit;
    }

    .logo-display {
      margin: 0 auto;
    }
}

main {
  position: relative;
  z-index: 2;
}

.body-dashboard-wrapper {
  z-index: 3;
}

.modal-wrapper {
  z-index: 4;
}

.today-indicator, .on-date {
  font-family: inherit;
  color: white;
  text-transform: uppercase;
}

.back-to-today {
  transform: scale(0.75);
  transition: all .2s;
  display: inline-block;
  border-radius: 5px;
  border: 2px solid $yellow;
  text-transform: uppercase;
  padding: 5px;

  &:hover {
    cursor: pointer;
    background-color: $yellow;
    color: $black;
  }
}

.threat-stage {
  display: flex;
  justify-content: center;
  transition: all .2s;

  p {
    max-width: 40rem;
    border-radius: 40px;
    background-color: rgba($lightgreen, 0.8);
    padding: 0.5rem 1rem;
    color: $darkgreen;
    transition: all .2s;
    box-shadow: 0px 5px 5px 0 rgba(black, 0.5);
  }

  small {
    text-transform: uppercase;
  }

  strong {
    font-family: $headerFont;
    margin-right: 0.5rem;
  }

  &.active-threats {
    p {
      background: rgba($red, 0.7);
      color: white;
    }
  }

  &.inviz {
    opacity: 0;
  }
}

.space-bg-stage {
  position: absolute;
  width: 150vw;
  height: 150vh;
  z-index: -1;
  transition: all 1s;
  opacity: 0.5;

  .space-bg {
    height: 100%;
    width: 100%;
  }
}

.about {
  padding-left: 1rem;

  ul {
    padding: 0;
    margin: 0;

    li {
      list-style: none;
      font-size: small;
    }
  }

  a {
    text-decoration: none;
    color: $yellow;
    transition: all .5s;

    &:hover {
      color: $lightgreen;
    }
  }
}

.mobile-stage {
  display: none;
}

@media only screen and (max-width: 1000px) {
  .mobile-stage {
    display: flex;
  }

  header,
  .date-editor,
  .threat-stage {
    display: none;
  }
}
</style>
