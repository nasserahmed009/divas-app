<template>
  <div class="app">
    <div class="app-container">
      <div>
        <img class="app-logo" src="@/assets/logo.png" alt="divas" />
      </div>
      <p class="homepage__subtitle">Rank Us</p>
      <h1 class="homepage__average-rate">{{ averageRate }}</h1>
      <RateApp @rate="setRating" />
    </div>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getDatabase, ref, update, onValue } from "firebase/database";

export default {
  name: "HomeView",

  mounted() {
    this.initializeFirebase();
    this.readAndUpdateTotalRating();
  },

  data() {
    return {
      database: null,
      averageRate: 0,
    };
  },

  components: {
    RateApp: () => import("@/components/RateApp.vue"),
  },

  methods: {
    generateRandomToken() {
      return (
        Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15)
      );
    },

    initializeFirebase() {
      const firebaseConfig = {
        apiKey: process.env.VUE_APP_API_KEY,
        authDomain: process.env.VUE_APP_AUTH_DOMAIN,
        databaseURL: process.env.VUE_APP_DATABASE_URL,
        projectId: process.env.VUE_APP_PROJECT_ID,
        storageBucket: process.env.VUE_APP_STORAGE_BUCKET,
        messagingSenderId: process.env.VUE_APP_MESSAGING_SENDER_ID,
        appId: process.env.VUE_APP_APP_ID,
      };

      // Initialize Firebase
      const app = initializeApp(firebaseConfig);
      const database = getDatabase(app);

      this.database = database;
    },

    setRating(rating) {
      const updates = {};
      const key = this.generateRandomToken();
      updates["/ratings/" + key] = rating;
      update(ref(this.database), updates);
    },

    readAndUpdateTotalRating() {
      const ratingsRef = ref(this.database, "ratings");

      onValue(ratingsRef, (snapshot) => {
        const data = snapshot.val();
        if (data) {
          this.averageRate = this.getAverageRating(Object.values(data));
        }
      });
    },

    getAverageRating(ratings) {
      const sum = ratings.reduce((a, b) => a + b, 0);
      const rating = sum / ratings.length;
      return Math.round(rating * 100) / 100;
    },
  },
};
</script>

<style lang="scss" scoped>
.app {
  margin: 0;
  padding: 5rem 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  background: rgba(255, 239, 248, 1);
  width: 100vw;
  height: 100vh;
}

.app-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 5rem 4rem;
  border-radius: 15px;
  background: rgb(255, 181, 215);
  background: linear-gradient(
    180deg,
    rgb(255, 181, 215) 0%,
    rgb(255, 181, 215) 21%,
    rgb(255 216 238 / 83%) 100%
  );
  box-shadow: 0 2.8px 2.2px rgb(249 181 215), 0 6.7px 5.3px rgb(249 184 217),
    0 12.5px 10px rgb(230 180 205), 0 22.3px 17.9px rgb(233 182 208),
    0 41.8px 33.4px rgb(254 238 247), 0 100px 80px rgb(254 239 248);
}

.app-logo {
  width: 150px;
}

.homepage__subtitle {
  color: #fff;
  font-size: 1.5rem;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  margin-bottom: 2rem;
}

.homepage__average-rate {
  color: #fff;
  font-size: 3rem;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  margin-bottom: 2rem;
}
</style>
