<template>
  <div class="about">
    <h1>Counter Reset</h1>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getDatabase, set, ref } from "firebase/database";

export default {
  mounted() {
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

    set(ref(database, "ratings"), {});
  },
};
</script>
