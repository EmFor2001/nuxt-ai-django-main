<script setup>
const searchQuery = ref("star");
const resultQuery = ref(null);
const arrayData = ref(null);
const loader = ref(false);
const searchData = async () => {
  loader.value = true;
  await useFetch(
    "https://images-api.nasa.gov/search?q=" + searchQuery.value
  ).then((response) => {
    if (response.data) {
      setTimeout(() => {
        resultQuery.value = response.data.value.collection.items;
        loader.value = false;
        console.log("respons", response.data.value.collection);
      }, 1000);
    }
  });
};

onBeforeMount(async () => {
  await searchData();
});

// Użycie watch do obserwacji resultQuery
watch(
  resultQuery,
  (newValue, oldValue) => {
    arrayData.value = newValue;

    console.log("arrayData", newValue); // Wyświetla nową wartość resultQuery.value
    // Możesz tutaj dodać dodatkowe logikę, która ma się wykonać po zmianie wartości
  },
  {
    deep: true, // opcja 'deep' jest przydatna, jeśli obserwowany obiekt jest złożony (np. obiekt lub tablica)
  }
);
</script>
<template>
  <div>
    <input type="text" v-model="searchQuery" />
    <button @click="searchData" v-if="!loader">Wyszukaj</button>
    <button @click="searchData" disabled v-else>Wyszukaj</button>
    <div class="cos" v-if="loader">To jest ladowanie danych</div>

    <div class="result" v-else>
      <div v-for="(value, index) in arrayData" :key="index">
        <div
          v-for="(link, linkIndex) in value.links"
          :key="`link-${index}-${linkIndex}`"
        >
          <img v-if="link.href.endsWith('.jpg')" :src="link.href" alt="Image" />
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped lang="scss">
h3 {
  span {
    color: green;
  }
}
.result {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  img {
    width: auto;
    height: 200px;
    margin: 10px;
  }
}
</style>
