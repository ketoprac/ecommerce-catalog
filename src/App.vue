<script setup>
import { computed, ref } from "vue";

const id = ref(1);
const data = ref([]);
const loading = ref(false);

const fetchData = async () => {
  loading.value = true;
  try {
    const response = await fetch(
      "https://fakestoreapi.com/products/" + id.value
    );
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    data.value = await response.json();
  } catch (error) {
    console.error("Error fetching data:", error);
    data.value = null;
  }
  loading.value = false;
};

const nextProduct = () => {
  if (id.value >= 20) {
    id.value = 1;
  } else {
    id.value++;
  }
  fetchData();
  console.log("next product");
};

const displayRating = computed(() => {
  if (!data.value.rating || !data.value.rating.rate) return "";

  const roundedValue = Math.round(data.value.rating.rate);
  const fullStars = "⬤".repeat(roundedValue);
  const emptyStars = "〇".repeat(5 - roundedValue);

  return `${fullStars}${emptyStars}`;
});

const invalidCategory = computed(() => {
  return (
    !loading.value &&
    data.value &&
    data.value.category !== "men's clothing" &&
    data.value.category !== "women's clothing"
  );
});

fetchData();
</script>

<template>
  <div class="bg">
    <div
      class="wrapper"
      :class="{
        invalid:
          data.category !== 'men\'s clothing' &&
          data.category !== 'women\'s clothing',
        men: data.category === 'men\'s clothing',
        women: data.category === 'women\'s clothing',
      }"
    >
      <div v-if="!loading">
        <div v-if="invalidCategory" class="container invalid">
          <p>This product is unavailable to show</p>
          <button class="secondary" @click="nextProduct">Next product</button>
        </div>
        <div v-else class="container">
          <div class="container__image">
            <img :src="data.image" alt="product image" />
          </div>
          <div>
            <h1
              class="title"
              :class="{
                men: data.category === 'men\'s clothing',
                women: data.category === 'women\'s clothing',
              }"
            >
              {{ data.title }}
            </h1>
            <div
              class="category"
              :class="{
                men: data.category === 'men\'s clothing',
                women: data.category === 'women\'s clothing',
              }"
            >
              <p class="category__title">{{ data.category }}</p>
              <p>{{ data.rating?.rate }}/5 {{ displayRating }}</p>
            </div>
            <hr />
            <p class="description">
              {{ data.description }}
            </p>
            <hr />
            <p
              class="price"
              :class="{
                men: data.category === 'men\'s clothing',
                women: data.category === 'women\'s clothing',
              }"
            >
              ${{ data.price }}
            </p>
            <div class="container__button">
              <button
                class="primary"
                :class="{
                  men: data.category === 'men\'s clothing',
                  women: data.category === 'women\'s clothing',
                }"
              >
                Buy now
              </button>
              <button
                class="secondary"
                :class="{
                  men: data.category === 'men\'s clothing',
                  women: data.category === 'women\'s clothing',
                }"
                @click="nextProduct"
              >
                Next product
              </button>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="container loading">
        <h1>Loading...</h1>
      </div>
    </div>
  </div>
</template>
