<template>
  <div>
    <header>
        <h1>Watchful Eye | NEOWS Watching <span class="count">{{ bodyCount }}</span> objects today</h1>
    </header>
    
    <main>
      <BodyDashboard 
        :bodyCollection="foundBodies" 
        :bodyCount="bodyCount"
        class="body-dashboard-wrapper"
      />
    </main>
    <Earth class="gaia" />
      
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
      var today = new Date();
      var dd = String(today.getDate()).padStart(2, '0');
      var mm = String(today.getMonth() + 1).padStart(2, '0');
      var yyyy = today.getFullYear();
      return `${yyyy}-${mm}-${dd}`;
    },
    dataUrl: function() {
      return `${this.apiUrl}start_date=${this.today}&end_date=${this.today}&api_key=${this.apiKey}`
    },
  },
  mounted() {
    this.getData();

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
</style>
