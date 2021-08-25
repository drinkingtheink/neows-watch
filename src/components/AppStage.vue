<template>
  <main>
    <h1>{{ msg }} | NEOWS Watching {{ bodyCount }} objects</h1>
  </main>
</template>

<script>
export default {
  name: 'AppStage',
  props: {
    msg: String
  },
  data() {
    return {
      apiUrl: 'https://api.nasa.gov/neo/rest/v1/feed?',
      apiKey: 'Z1Y1LoyLfSDcQJI51fl8E1tYsrZQgnvU09C5pV36',
      bodyCount: 0,
    }
  },
  computed: {
    today: function() {
      return `2021-08-24`;
    },
    dataUrl: function() {
      return `${this.apiUrl}start_date=${this.today}&api_key=${this.apiKey}`
    }
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
        })
        .catch(error => {
          console.error('There has been a problem with your fetch operation:', error);
        });
      }
    }
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

</style>
