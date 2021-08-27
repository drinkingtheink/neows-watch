<template>
  <div>
    <header>
        <h1 v-if="searching">Searching the Skies...</h1>
        <h1 v-else>
          WATCHFUL EYE | NEOWS Watches 
          <span class="count">{{ bodyCount }}</span> 
          objects 
          <span v-if="todaySearchIsActive" class="today-indicator">today</span>
          <span v-else class="on-date">on {{ dateToSearch }}</span>
        </h1>
        <button v-if="!todaySearchIsActive" class="back-to-today" @click="setSearchToToday">Back to Today</button>
    </header>
    
    <main>
      <BodyDashboard 
        :bodyCollection="foundBodies" 
        :bodyCount="bodyCount"
        class="body-dashboard-wrapper"
      />
    </main>
    <Earth class="gaia" />

    <section class="date-editor">
      <small>Explore Other Dates Using Your Up and Down Arrows or by Typing: </small>
      
      <label for="year">Year</label>
      <input 
        v-model.number="userStartYear" 
        type="number"
        id="year" 
        name="year"
        min="1900" 
        max="2600"
        maxlength="4" />

      <label for="month">Month</label>
      <input 
        v-model.number="userStartMonth" 
        type="number"
        id="month" 
        name="month"
        min="01" 
        max="12"
        maxlength="2" />

      <label for="day">Day</label>
      <input 
        v-model.number="userStartDay" 
        type="number"
        id="day" 
        name="day"
        min="01" 
        max="32"
        maxlength="2" />
        
    </section>
      
    <Modal
      v-show="modalIsVisible"
      @close="closeModal"
      :content="modalContent"
      class="modal-wrapper"
    />
  </div>
</template>

<script>
import BodyDashboard from './BodyDashboard.vue';
import Modal from './Modal.vue';
import Earth from './Earth.vue'
import { EventBus } from '../EventBus';

export default {
  name: 'AppStage',
  components: {
    BodyDashboard,
    Modal,
    Earth,
  },
  data() {
    return {
      apiUrl: 'https://api.nasa.gov/neo/rest/v1/feed?',
      apiKey: 'Z1Y1LoyLfSDcQJI51fl8E1tYsrZQgnvU09C5pV36',
      searching: false,
      searchError: false,
      bodyCount: 0,
      foundBodies: null,
      modalIsVisible: false,
      modalContent: null,
      userStartYear: null,
      userStartMonth: null,
      userStartDay: null,
    }
  },
  computed: {
    today: function() {
      return new Date();
    },
    currentDay: function() {
      return String(this.today.getDate()).padStart(2, '0');
    },
    currentMonth: function() {
      return String(this.today.getMonth() + 1).padStart(2, '0');
    },
    currentYear: function() {
      return this.today.getFullYear();
    },
    searchToday: function() {
      return `${this.currentYear}-${this.currentMonth}-${this.currentDay}`;
    },
    dateToSearch: function() {
      return this.newDateReady ? `${this.userStartYear}-${this.userStartMonth}-${this.userStartDay}` : this.searchToday;
    },
    dataUrl: function() {
      return `${this.apiUrl}start_date=${this.dateToSearch}&end_date=${this.dateToSearch}&api_key=${this.apiKey}`
    },
    newDateReady: function() {
      return this.userStartYear && this.userStartMonth && this.userStartDay;
    },
    todaySearchIsActive: function() {
      return this.searchToday === this.dateToSearch;
    },
  },
  watch: {
    newDateReady: function () {
      this.getData();
    },
    userStartYear: function () {
      if (this.newDateReady) this.getData();
    },
    userStartMonth: function () {
      if (this.newDateReady) this.getData();
    },
    userStartDay: function () {
      if (this.newDateReady) this.getData();
    },
  },
  mounted() {
    this.getData();

    this.userStartYear = this.currentYear;
    this.userStartMonth = this.currentMonth;
    this.userStartDay = this.currentDay;

    EventBus.$on('body-selected', (payload) => {
      this.modalContent = payload;
      this.showModal();
    });
  },
  methods: {
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
    getData() {
      this.searching = true;
      this.searchError = false;
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
          this.searching = false;
        })
        .catch(error => {
          console.error('There has been a problem with your fetch operation:', error);
          this.searching = false;
          this.searchError = true;
        });
      }
    },
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';

.date-editor {
  border-top: 2px solid $yellow;
  z-index: 4;
  background-color: rgba(black, 0.8);
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
  }
}

header {
    display: flex;
    justify-content: center;
    align-content: center;
    background-color: rgba(black, 0.4);

    h1 {
        font-size: 110%;
    }

    .count {
      color: white;
      font-family: inherit;
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
  transform: scale(0.5);
}
</style>
