<template>
<div>
    <div class="tabs is-centered is-toggle is-large is-fullwidth">
        <ul>
            <li v-for="(tab, index) in tabs" :key="index" :class="{ 'is-active': tab.isActive }">
                <a @click="selectTab(tab)">
                    <span>{{tab.name}}</span>
                </a>
            </li>
        </ul>
    </div>
    <div class="tabs-details">
        <slot></slot>
    </div>
</div>
</template>
<<script>
import EventBus from '../eventBus';
import Tab from './Tab.vue';
export default {
    name: 'Tabs',
    data() {
        return {
            tabs: []
        }
    },
    created() {
        this.tabs = this.$children;
    },
    methods: {
        selectTab(selectedTab) {
            this.tabs.forEach(tab => {
                tab.isActive = (tab.name === selectedTab.name);
                console.log(selectedTab)
                EventBus.$emit('selectTab', selectedTab.$attrs.attr);
            });
        }
    }
}
</script>
<style scoped>
    .tabs {
        margin: 0;
    }
    .is-active a{
        background-color: #e4498e !important;
        border-color: #e4498e !important;
    }

    li a span {
        font-weight: bold;
    }
</style>
