<template>
  <div
    class="column is-3"
    @click="$emit('click-me', { id: card.id, uid: card.uid })"
  >
    <div class="card">
      <img :src="`https://picsum.photos/id/${card.id}/350/350`" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'Card',
  props: {
    card: Object,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card {
  position: relative;
}

.card:after {
  position: absolute;
  width: 100%;
  height: 100%;
  background: grey;
  top: 0;
  left: 0;
  content: '';
}

.visible .card:after {
  display: none;
}

.done {
  animation: fadeOut 1s linear 1s forwards;
}

@keyframes fadeOut {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>
