<template>
    <div v-if="loading" class="spinner" :style="{height: size, width: size}"></div>
</template>

<script>
export default {
    name: 'Spinner',
    props: {
        loading: Boolean,
        size: {
            default: '18px',
            type: String
        }
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    @keyframes spin {
        to { -webkit-transform: rotate(360deg); }
    }
    @-webkit-keyframes spin {
        to { -webkit-transform: rotate(360deg); }
    }
    .spinner {
        margin: 0 3px;
        display: inline-block;
        border: 3px solid rgba(255,255,255,.3);
        border-radius: 50%;
        border-top-color: #fff;
        animation: spin 1s ease-in-out infinite;
        -webkit-animation: spin 1s ease-in-out infinite;
    }
</style>
