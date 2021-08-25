<template>
  <main>
    <h1>{{ msg }} | NEOWS Watching {{ bodyCount }} objects today</h1>
    <BodyDashboard 
      :bodyCollection="foundBodies" 
      :bodyCount="bodyCount"
    />
  </main>
</template>

<script>
import BodyDashboard from './BodyDashboard.vue'

export default {
  name: 'AppStage',
  props: {
    msg: String
  },
  components: {
    BodyDashboard,
  },
  data() {
    return {
      apiUrl: 'https://api.nasa.gov/neo/rest/v1/feed?',
      apiKey: 'Z1Y1LoyLfSDcQJI51fl8E1tYsrZQgnvU09C5pV36',
      bodyCount: 0,
      foundBodies: null,
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
  },
  methods: {
    getData() {
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
        })
        .catch(error => {
          console.error('There has been a problem with your fetch operation:', error);
        });
      }
    },
}
</script>

<style lang="scss" scoped>
@import '../styles/palette';

h1 {
  color: $yellow;
}

h3 {
  margin: 40px 0 0;
  color: white;
}

main {
  position: relative;
}
</style>
