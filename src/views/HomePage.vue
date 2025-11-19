<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title><center>Crypto Price Tracker</center></ion-title>
      </ion-toolbar>
    </ion-header>


    <ion-content :fullscreen="true">
      <div id="container">
        <ion-button expand="block" @click="fetchData">Refresh</ion-button>


        <ion-list v-if="tickers.length">
          <ion-item v-for="ticker in tickers" :key="ticker.id">
            <ion-label>
              <p>Rank: {{ ticker.rank }}</p>
              <h3>{{ ticker.name }}</h3>
              <h2>{{ ticker.symbol }}</h2>
            </ion-label>
            <ion-note slot="end" color="dark">
              <p>USD</p>
              <h1>${{ formatPrice(ticker.price_usd) }}</h1>
            </ion-note>
          </ion-item>
        </ion-list>


        <div v-else-if="isLoading">
          <p>Memuat data...</p>
        </div>
        <div v-else-if="error">
          <p style="color: red;">Terjadi kesalahan: {{ error }}</p>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import {
  IonContent, IonHeader, IonPage, IonTitle, IonToolbar,
  IonList, IonItem, IonLabel, IonNote, IonButton,
} from '@ionic/vue';
import axios from 'axios';


const tickers = ref([]);
const isLoading = ref(false);
const error = ref(null);
const API_URL = 'https://api.coinlore.net/api/tickers/';


// Fungsi ambil data API
const fetchData = async () => {
  isLoading.value = true;
  error.value = null;
  tickers.value = [];


  try {
    const response = await axios.get(API_URL);


    // Ambil 10 data teratas
    if (response.data && response.data.data) {
      tickers.value = response.data.data.slice(0, 10).map(item => ({
        id: item.id,
        rank: item.rank,
        name: item.name,
        symbol: item.symbol,
        price_usd: item.price_usd,
      }));
    } else {
      throw new Error('Format data API tidak valid.');
    }
  } catch (err) {
    error.value = err.message || 'Gagal terhubung.';
  } finally {
    isLoading.value = false;
  }
};


// Fungsi format harga
const formatPrice = (price) => {
    const num = parseFloat(price);
    // Tampilkan desimal yang cukup
    return num.toFixed(num >= 1 ? 2 : 6).toLocaleString('en-US');
};


// Panggil saat komponen dimuat pertama kali
onMounted(() => {
  fetchData();
});
</script>
<style scoped> 
#container {
  padding: 16px;
}


/* Styling item list */
ion-item {
  --background: #fff8e1;
  --border-radius: 8px;
  margin-bottom: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}


/* Styling Rank */
ion-label p {
  font-size: 1em;
  margin: 0 0 4px 0;
  color: #000;
}


/* Styling Nama Koin (Bitcoin, Ethereum) */
ion-label h3 {
  font-size: 1em;
  font-weight: normal;
  margin-top: 2px;
  color: #666;
}


/* Styling Simbol Koin (BTC, ETH) */
ion-label h2 {
  font-size: 1em;
  font-weight: bold;
  margin: 0;
  color: #333;
}


/* Styling Harga USD */
ion-note {
  text-align: right;
  display: block;
}


/* Styling teks 'USD' */
ion-note p {
  font-size: 2em;
  margin: 0 0 2px 0;
  color: #666;
}


/* Styling Nilai Harga */
ion-note h1 {
  font-size: 1.7em;
  font-weight: bold;
  margin: 0;
  color: #000;
}


/* Styling Tombol Refresh */
ion-button {
  margin-bottom: 16px;
  --background: #007bff;
}

</style>