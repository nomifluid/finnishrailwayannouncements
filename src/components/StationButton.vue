<template>
  <div>
    <input v-model="selected" @click="selected ? $emit('remove', name) : $emit('add', name)" type="checkbox"
      :name="name" :id="name">
    <label :for="name">{{ name }}</label>
  </div>
</template>

<script>
export default {
  props: ['name'],
  emits: ['add', 'remove'],
  data() {
    return {
      selected: false
    }
  }
}
</script>