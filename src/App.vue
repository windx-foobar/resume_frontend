<template>
  <div>
    <pre>
      {{ PrettyPrint }}
    </pre>
    <button @click="showComponent = !showComponent">Test</button>
    <Marks
      @update:tags="currentValue = $event"
      v-if="showComponent"
    />
  </div>
</template>

<script>
import Marks from './components/Marks';

export default {
   components: { Marks },
   data: () => ({
      currentValue: [],
      showComponent: true
   }),
   computed: {
      PrettyPrint() {
         return JSON.stringify(this.currentValue);
      }
   }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}

.sh {
  border: 1px solid;
  display: flex;
  align-items: center;

  .tag {
    height: 2rem;
    padding: 0.25rem 0.5rem;
    background: #97e7fb;
    margin: 0.25rem;
    display: flex;
    align-items: center;
    white-space: nowrap;
  }

  input {
    margin: 0.25rem;
    border: 1px solid;
    height: 2rem;
    padding: 0.25rem 0.5rem;
    background: yellow;
    display: flex;
    flex: 1;
  }
}

.list {
  padding: 0;
  margin: 0;
  list-style: none;

  li {
    list-style: none;
    padding: 0.25rem 0.5rem;
    border: 1px solid;
  }
}
</style>