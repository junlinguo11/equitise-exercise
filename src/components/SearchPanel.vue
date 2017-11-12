<template>
    <nav class="panel">
    <p class="panel-heading">
        Search
    </p>
    <div class="panel-block">
        <p class="control has-icons-left">
        <input class="input is-medium" type="text" placeholder="Enter keywords" v-model="keyword" @input="onInput">
        <span class="icon is-small is-left">
            <i class="fa fa-search"></i>
        </span>
        </p>
    </div>
    </nav>
</template>
<script>
import EventBus from '../eventBus';
export default {
    name: 'SearchPanel',
    data() {
        return{
            keyword: ''
        }
    },
    methods: {
        onInput: function() {
            EventBus.$emit('search', this.keyword);
        }
    }

}
</script>
<style scoped>
    .panel {
        background-color: white;
    }
</style>
