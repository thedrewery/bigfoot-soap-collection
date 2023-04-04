<template>
  <div>
    <h2>Filter Bundles By Scent:</h2>
    <div v-for="(scent, index) in this.scents" :key="index" class="filter">
      <div>
        <input
          type="checkbox"
          :value="scent"
          class="checkbox"
          @change="addSelectedScents(scent)"
        />
        <span class="filtered-scent">{{ scent }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ScentFilter',
  props: {
    scents: []
  },
  data() {
    return {
      selectedScents: []
    }
  },
  methods: {
    emitUpdateScents() {
      this.$emit('updateScents', { data: this.selectedScents })
    },
    addSelectedScents(scent) {
      if (this.selectedScents.includes(scent)) {
        let scentIndex = this.selectedScents.indexOf(scent)
        this.selectedScents.splice(scentIndex, 1)
      } else {
        this.selectedScents.push(scent)
      }
      this.emitUpdateScents()
    }
  },
}
</script>

<style>
.filter {
  display: inline-block;
  text-transform: uppercase;
  margin: 4px;
  padding: 4px;
  font-size: 20px;
}
</style>
