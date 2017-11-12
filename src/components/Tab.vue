<template>
    <div v-show="isActive"><slot></slot></div>
</template>
<script>
export default {
    name: 'Tab',
    props: {
        name: { required: true },
        selected: { default: false }
    },
    data() {
        return {
            isActive: false
        };
    },
    mounted() {
        this.isActive = this.selected;
        if(window.sr) {
            window.sr.reveal('.box', { reset: true, duration: 1000 }, 40);
        }
    }
}    
</script>

