<template lang="html">
  <property
    v-if="!!resolvedSchema"
    :schema="resolvedSchema"
    :model-root="modelWrapper.root"
    :model-wrapper="modelWrapper"
    :options="fullOptions"
    :firsttime="initialLoad"
    model-key="root"
    parent-key=""
    @error="e => $emit('error', e)"
    @change="e => $emit('change', e)"
    @input="e => $emit('input', e)"
    @typechange="e => $emit('typechange', e)"
    :isNewform="isNewform"
  />
</template>

<script>
import jrefs from './utils/json-refs'
import colors from './utils/colors'
import Property from './components/Property.vue'
export default {
  name: 'VJsonschemaForm',
  components: { Property },
  props: ['schema', 'model', 'options', 'isNewform'],
  data() {
    return { modelWrapper: { root: this.model } }
  },
  computed: {
    initialLoad(){
      localStorage.isNewForm = this.isNewform;
      if(Object.keys(this.modelWrapper.root).length  != 0){
        localStorage.isNewForm = false;
      }
      return Object.keys(this.modelWrapper.root).length  === 0 ? localStorage.firsttime= true : localStorage.firsttime= false;
    },
    resolvedSchema() {
      return jrefs.resolve(this.schema)
    },
    fullOptions() {
      const httpLib = this.axios || this.$http || this.$axios
      return Object.assign({}, {
        debug: false,
        httpLib,
        disableAll: false,
        colors,
        autoFoldObjects: false,
        requiredMessage: 'This information is required',
        noDataMessage: 'No matching value found',
        searchMessage: 'Search...',
        // we use the multi-themes functionality of vuetify2 as a clue of the version
        vuetifyVersion: !this.$vuetify.theme.themes ? 1 : 2
      }, this.options)
    }
  }
}
</script>

