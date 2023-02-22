<script lang="ts">
import { PropType } from 'vue';

export default {
  props: {
    joke: {
      type: Object,
      required: true
    },
    onClose: {
    type: Function as PropType<(e: MouseEvent) => void>,
    required: true
  },
  },
  methods: {
    toggleFavorite() {
      this.$emit('toggle-favorite', this.joke);
    }
  }
};
</script>

<template>
  <div class="modal">
    <div class="modal-content">
      <div v-if="joke.type === 'single'">
        <h3>{{ joke.joke }}</h3>
        
      </div>
      <div v-if="joke.type === 'twopart'">
        <h2>{{ joke.setup }}</h2>
        <p>{{ joke.delivery }}</p>
      </div>
      <button class="modal-close" @click="onClose($event)">Close</button>
 
      <button class="modal-favorite" @click="$emit('toggle-favorite', joke)">
        {{ joke.favorite ? 'Remove' : 'Favorite' }}
      </button>

    </div>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 9999;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: #463a3a;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  text-align: center;
}

.modal-close {
  margin-top: 20px;
  padding: 10px 20px;
  background: #100f0f;
  color: #ffffff !important;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 20px;
}
.modal-favorite {
  margin-top: 20px;
  padding: 10px 20px;
  background: #27bb6f;
  color: #ffffff !important;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 20px;
}
</style>
