<template>
  <div
    class="container board mt-5 has-text-centered"
    :style="{ maxWidth: 'calc(100vh - 8rem)' }"
  >
    <div class="columns is-vcentered">
      <div class="column is-9">
        <Progress :cols="cols" :done="done" />
      </div>
      <div class="column is-3">
        <div class="field is-grouped is-grouped-right">
          <p class="control">
            <a class="button is-light" @click="cols > 5 ? (cols -= 2) : null">
              -
            </a>
          </p>
          <p class="control">
            <input
              class="input"
              :style="{ textAlign: 'center', maxWidth: '60px' }"
              type="number"
              min="3"
              max="12"
              v-model="cols"
              disabled
            />
          </p>
          <p class="control">
            <a class="button is-light" @click="cols < 5 ? (cols += 2) : null">
              +
            </a>
          </p>
        </div>
      </div>
    </div>

    <div class="columns is-multiline is-mobile" v-if="notDone">
      <Card
        v-for="(card, index) in photos"
        v-bind:key="index"
        v-bind:card="card"
        v-bind:cols="cols"
        v-on:click-me="handleClick"
        :class="{
          visible: visible.some((v) => v.uid === card.uid),
          done: done.some((id) => id === card.id),
        }"
      />
    </div>
    <div v-else>
      <h1 class="title is-1 mt-6">Congratulations!!!</h1>
      <h3 class="subtitle is-3">Your score: {{ counter }} moves</h3>
      <button class="button is-primary" v-on:click="newRound">New round</button>
    </div>
  </div>
</template>

<script>
import Card from './Card';
import Progress from './Progress';

export default {
  name: 'Board',
  data() {
    return {
      cols: 4,
      counter: 1,
      photos: [],
      photosPage: 1,
      visible: [],
      done: [],
    };
  },
  created() {
    this.fetchPhotos();
  },
  computed: {
    notDone: function() {
      return this.done.length * 2 < Math.pow(this.cols, 2);
    },
  },
  watch: {
    cols: function() {
      this.counter = 1;
      this.done = [];
      this.visible = [];
      this.fetchPhotos();
    },
  },
  methods: {
    fetchPhotos: function() {
      fetch(
        `https://picsum.photos/v2/list?limit=${Math.pow(this.cols, 2) /
          2}&page=${this.photosPage}`
      )
        .then((res) => res.json())
        .then((json) => {
          const photos = [...json, ...json]
            .map((p, i) => {
              const copy = { ...p };
              copy.uid = i;
              return copy;
            })
            .sort(() => 0.5 - Math.random());
          this.photos = photos;
        });
    },
    handleClick: function({ id, uid }) {
      if (this.visible.length === 2) {
        this.visible = [];
        this.counter++;
      }
      if (!this.visible.some((v) => v.uid === uid)) {
        this.visible.push({ uid, id });

        if (this.visible.length > 1 && this.visible.every((v) => v.id === id)) {
          this.done.push(id);
        }
      }
    },
    newRound: function() {
      this.counter = 1;
      this.done = [];
      this.visible = [];
      this.photos = [];
      this.photosPage++;
      this.fetchPhotos();
    },
  },
  components: {
    Card,
    Progress,
  },
};
</script>

<style scoped></style>
