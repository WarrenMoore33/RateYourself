<template>
  <section>
    <div v-if="error && errorIsUp" class="errorModal">
      <p>**ERROR* For Developers Only **ERROR**<br>See Console<br><button @click="confirmError">OK, got it</button></p>
      <p>ERROR TYPE:<br><br>{{ error }}</p>
    </div>
    <base-card>
      <h2>Basketball Ratings</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <div class="lds-ripple" v-if="isLoading && !timeIsUp"><div></div><div></div></div>
      <p v-else-if="!isLoading && (!results || results.length === 0) && !timeIsUp"><strong>No Surveys Available</strong> as of {{ date }}</p>
      <p v-else-if="timeIsUp">Taking too long to load. Please refresh and try again</p>
      <ul v-else-if="!isLoading && results && results.length > 0">
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
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      timeIsUp: false,
      error: null,
      errorIsUp: false,
      date: new Date()
    }
  }, 
  methods: {
    setLoadingTimer() {
      setTimeout(() => {
          this.timeIsUp = this.isLoading ? true : false
          console.log('time is up is set to ' + this.timeIsUp);
      }, 5000);
    },
    loadExperiences() {
      // Setting the load icon then checking to see if it stays longer than 3 seconds
      this.timeIsUp = false;
      this.isLoading = true;
      this.error = null;
      fetch('https://vue-http-demo-5ecb9-default-rtdb.firebaseio.com/surveys.json').then((response) => {
        if (response.ok) {
          return response.json();
        }
      }).then((data) => {
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
      .catch((error) => {
        this.errorIsUp = true;
        this.error = 'Failed to fetch data - please try again later' + error;
      });
      this.setLoadingTimer();
    },
    confirmError() {
      this.errorIsUp = false;
    }
  },
  mounted() {
    this.setLoadingTimer();
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
.errorModal {
  background: rgba(0,0,0, .5);
  position: fixed;
  top: 0;
  width: 100%;
  height: 100vw;
}
.errorModal p {
  width: 600px;
  height: fit-content;
  text-align: center;
  margin: 0 auto;
  color: white;
  background: #360032a3;
  border-radius: 8px;
  padding: 80px 0;
  position: absolute;
  left: calc(50% - 240px);
  top: calc(50vh - 400px);
  font-weight: bold;
}
.errorModal button {
  color: white;
  background: transparent;
  border: 2px solid white;
  padding: 15px 25px;
  margin-top: 30px;
  border-radius: 8px;
}
.errorModal button:hover {
  background: white;
  color: black;
  transition: .3s;
  cursor: pointer;
}
.errorModal p:last-child {
  left: calc(50% - 240px);
  top: calc(50vh - 100px);
}

/* loading icon */
.lds-ripple {
  display: block;
  position: relative;
  width: 100%;
  height: 80px;
  margin: 20px 0;
  text-align: center;
  left: calc(50% - 40px);
}
.lds-ripple div {
  position: absolute;
  border: 4px solid #360032;
  opacity: 1;
  border-radius: 50%;
  animation: lds-ripple 1s cubic-bezier(0, 0.2, 0.8, 1) infinite;
}
.lds-ripple div:nth-child(2) {
  animation-delay: -0.5s;
}
@keyframes lds-ripple {
  0% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 0;
  }
  4.9% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 0;
  }
  5% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 1;
  }
  100% {
    top: 0px;
    left: 0px;
    width: 72px;
    height: 72px;
    opacity: 0;
  }
}

</style>