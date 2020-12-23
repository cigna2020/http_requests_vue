<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="errorMessage">
        Unfortunately, you face with next error: {{ errorMessage }}
      </p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experience found. Start adding some survey result first.
      </p>
      <ul
        v-else-if="!isLoading && results && results.length > 0 && !errorMessage"
      >
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  //   props: ['results'],
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: false,
      errorMessage: null
    };
  },
  methods: {
    loadExperiences() {
      // method GET - a default method that doesn't require header and body
      (this.isLoading = true),
        (this.errorMessage = null),
        fetch(
          'https://vue-http-demo-30c54-default-rtdb.firebaseio.com/surveys.json'
        )
          .then(response => {
            if (response.ok) {
              return response.json();
            }
          })
          .then(data => {
            this.isLoading = false;
            const results = [];
            for (const id in data) {
              results.push({
                id: id,
                name: data[id].name,
                rating: data[id].rating
              });
            }
            this.results = results;
          })
          .catch(error => {
            this.isLoading = false;
            this.errorMessage = error;
          });
    }
  },
  // The mounted() загружает ранее загруженные данные при перезагрузке страницы
  mounted() {
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
