<template>
    <div class="dropdown-item" @click="$emit('click', item)">{{item}}</div>
</template>
  
<script>
    export default {
        name: 'VueAutocompleteItem',
        props: {
            item: Object
        }
    }
</script>

<style scoped>

</style>
  