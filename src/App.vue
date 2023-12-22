<template>
  <div>
    <figure>
      <img src="/vueschool-logo.png" alt="VueSchool Logo" />
    </figure>
    <h1>Random Wise Quotes</h1>

    <button @click="sortQuotes">
      Sort By Rating ({{ sortOrder.toUpperCase() }})
    </button>
    <button @click="refreshQuotes" :disabled="!quotes.length">
      Get New Quotes
    </button> 

    <TransitionGroup name="list" tag="ul">
      <li v-for="quote in quotes" :key="quote._id">
        <span>Rating: {{ quote.rating }}</span>
        <blockquote>
          <p>{{ quote.content }}</p>
          <cite>- {{ quote.author }}</cite>
        </blockquote>
      </li>
    </TransitionGroup>
  </div>
</template>

<script setup>
import { ref, onMounted,onBeforeMount, watch } from 'vue';
const sortOrder = ref('desc');

const quotes = ref([]);

const fetchQuotes = async () => {
  console.log(sortOrder.value)
  const response = await fetch('https://api.quotable.io/quotes/random?limit=5');
  const data = await response.json();
  quotes.value = data.map((quote) => ({
    ...quote,
    rating: Math.floor(Math.random() * 20) + 1,
  }));

};

const refreshQuotes = () => {
  fetchQuotes();
};


onMounted(() => {
  fetchQuotes();
});



const sortQuotes = () => {
  console.log(sortOrder.value)
  if (sortOrder.value === 'desc') {
    quotes.value.sort((a, b) => b.rating - a.rating);
    sortOrder.value = 'asc'
  } else {
    quotes.value.sort((a, b) => a.rating - b.rating);
    sortOrder.value = 'desc'
  }
};


// // watch to sort the quotes
// watch(quotes, () => {
//   sortQuotes();
// });

// watch  sortOrder
watch(sortOrder, () => {
  sortOrder.value = sortOrder.value === 'desc' ? 'asc' : 'desc';
  sortQuotes();

});
</script>

<style>
figure {
  display: flex;
  justify-content: center;
  align-items: center;
  max-width: 200px;
  height: 45px;
  background-color: white;
  padding: 10px;
  border-radius: 30px;
  overflow: hidden;
  margin: 0 auto;
}

img {
  width: 100%;
}

h1 {
  color: #fc6c94;
}

button {
  margin-bottom: 10px;
  background-color: #00bda7;
  margin: 5px;
}

button:hover {
  background-color: #00b49c;
}

ul {
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 15px;
  place-items: center;
  justify-items: center;
  width: 100%;
}

li {
  color: white;
  background-color: #00c3fc;
  overflow: hidden;
  border-radius: 1rem;
  max-width: 600px;
  width: 100%;
  border-color: white;
  border: 1px;
  position: relative;
}

span {
  display: block;
  background-color: #342c8c;
  padding-top: 10px;
  padding-bottom: 10px;
}

blockquote {
  padding-top: 5px;
  padding-bottom: 10px;
}

p {
  font-weight: bold;
}

.list-move,
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.list-leave-active {
  position: absolute;
}
</style>
