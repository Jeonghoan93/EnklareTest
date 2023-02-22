<script lang="ts">
import axios from 'axios';
import { defineComponent } from 'vue';
import JokeModal from './JokeModal.vue';

import Joke from './interfaces/Joke'

export default defineComponent({
    data() {
        return {
            jokes: [] as Joke[],
            loading: true,
            selectedJoke: null as Joke | null,
            filter: "any",
            favoriteMessage: '',
        };
    },
    mounted() {
        this.fetchJokes();
    },
    methods: {
        async fetchJokes() {
            const url = "https://v2.jokeapi.dev/joke/Programming,Pun,Misc,Spooky?safe-mode&amount=10";
            try {
                const response = await axios.get(url);
                this.jokes = response.data.jokes.map((joke: any) => {
                    return { ...joke, id: Date.now() + Math.random(), favorite: false };
                });
                this.loading = false;
            }
            catch (error) {
                console.error(error);
            }
        },
        openModal(joke: Joke) {
            this.selectedJoke = joke;
        },
        closeModal() {
            this.selectedJoke = null;
        },
        toggleFavorite(joke: Joke) {
          joke.favorite = !joke.favorite;
          this.favoriteMessage = joke.favorite
            ? 'Joke added to favorites'
            : 'Joke removed from favorites';
          setTimeout(() => {
            this.favoriteMessage = '';
          }, 5000);
        },
      },
          computed: {
        visibleJokes() {
            if (this.filter === "single") {
                return this.jokes.filter((joke: Joke) => joke.type === "single");
            }
            else if (this.filter === "twopart") {
                return this.jokes.filter((joke: Joke) => joke.type === "twopart");
            }
            else {
                return this.jokes;
            }
        },
        hiddenCount() {
            return this.jokes.length - this.visibleJokes.length;
        },
        jokeCount() {
            return `${this.visibleJokes.length}/${this.jokes.length}`;
        },
        jokeTypes() {
            const types: any = {};
            this.jokes.forEach((joke: Joke) => {
                if (!types[joke.type]) {
                    types[joke.type] = 0;
                  }
                    types[joke.type]++;
                    });
                    return types;
                    },
        favoriteJoke() {
            return this.jokes.find(joke => joke.favorite);
            },
            },
            components: { JokeModal }
            });
</script>

<template>
  <div class="joke-container">

    <select v-model="filter" class="filter-select">
      <option value="any">Any</option>
      <option value="single">Single</option>
      <option value="twopart">Two-Part</option>
    </select>

    <div class="joke-count">
      <p>{{ jokeCount }} jokes</p>
      <p class="hidden" v-if="hiddenCount > 0">{{ hiddenCount }} jokes hidden by filter</p>
    </div>
    <ul v-if="!loading" class="joke-list">

      <li v-for="(joke, index) in visibleJokes" :key="index" :class="[joke.category, { 'favorite': joke === favoriteJoke }]">
        <div class="joke-content">
 
            <p v-if="joke.type === 'twopart'">{{ joke.setup?.split(' ').slice(0, 3).join(' ') }}<span v-if="joke.setup && joke.setup?.split(' ').length > 3">...</span></p>
            <p v-if="joke.type ==='single'">{{ joke.joke?.split(' ').slice(0, 3).join(' ') }}<span v-if="joke.joke && joke.joke?.split(' ').length > 3">...</span></p>
        
            <button @click="openModal(joke)">Show more</button>
          
        </div>
 
        <div class="favorite-message" :class="{ show: !!favoriteMessage }">
          {{ favoriteMessage }}
        </div>
      </li>
    </ul>
    <div v-else>
      Loading...
    </div>
    
    <button class="refresh" @click="fetchJokes">Refresh Jokes</button>

    <JokeModal v-if="selectedJoke" :joke="selectedJoke" :onClose="closeModal" @toggle-favorite="toggleFavorite"/>

  </div>
</template>

<style scoped>
.joke-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.filter-select {
  margin-top: 2rem;
  margin-bottom: 1rem;
  color: #ffffff !important;
}

.joke-count {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 1rem;
}
.joke-count p {
  margin-right: 1rem;
}

.hidden {
  opacity: 0.7;
}

.joke-list {
  list-style: none;
  padding: 0;
}

.joke-list li {
  margin-bottom: 1rem;
}

.joke-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.joke-content p {
  margin-right: 1rem;
}

.joke-content button {
  background-color: #1a1a1a;
  color: #f0e8e8;
  border-radius: 8px;
  border: none;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  cursor: pointer;
  transition: background-color 0.25s, color 0.25s;
}

.joke-content button:hover {
  background-color: #ffffff;
  color: #1a1a1a;
  border: 1px solid #1a1a1a;
}
.favorite-message {
    display: none;
    position: fixed;
    top: 1rem;
    right: 1rem;
    background-color: yellow;
    padding: 1rem;
    border-radius: 5px;
    transition: all 0.5s;
  }
.favorite-message.show {
    display: block;
  }
.refresh {
  margin-top: 1rem;
  margin-bottom: 3rem;
  background-color: #209d20;
  color: #f9f9f9f9;
  border-radius: 8px;
  border: none;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  cursor: pointer;
  transition: background-color 0.25s, color 0.25s;
}
.Programming {
  color: rgb(122, 145, 236);
}

.Pun {
  color: red;
}

.Misc {
  color: rgb(144, 234, 144);
}

.Spooky {
  color: rgb(215, 110, 215);
}

</style>