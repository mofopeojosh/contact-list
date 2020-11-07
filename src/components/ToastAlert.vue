<template>
    <div v-if="alertMessage" class="toast-alert">
        <div class="toast-alert-content" :class="className">
            <div>
                <span class="indicator"></span>
            </div>
            <span class="toast-alert-message">{{ alertMessage }}</span>
            <a class="link" href="#" @click.prevent="hideMessage">x</a>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ToastAlert',
    props: {
        message: String,
        type: String,
    },
    data() {
        return {
            className: '',
            alertMessage: '',
            timeout: null,
        };
    },
    watch: {
        message() {
            this.showMessage();
        },
    },
    mounted() {
        this.showMessage()
    },
    methods: {
        showMessage() {
            if(this.message) {
                clearTimeout(this.timeout);
                this.className = `alert-${this.type}`;
                this.alertMessage = this.message;
                this.timeout = setTimeout(() => {
                    this.hideMessage();
                }, 5000);
            }
        },
        hideMessage() {
            clearTimeout(this.timeout);
            this.className = '';
            this.alertMessage = '';
            this.$emit('hide')
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .toast-alert {
        position: fixed;
        top: 50px;
        width: 250px;
        padding: 8px 10px;
        left: 0;
        right: 0;
        margin: auto;
        border-radius: 8px;
        background-color: #FFFFFF;
        border: 1px solid #f5f5f5;
        box-shadow: 0 1px 10px 1px rgba(0, 0, 0, .1);
    }

    .toast-alert .toast-alert-content {
        font-size: 12px;
        display: flex;
    }

    .toast-alert a {
        color: inherit;
    }

    .toast-alert .indicator {
        display: inline-block;
        height: 8px;
        width: 8px;
        border-radius: 50%;
        background-color: orange;
    }

    .toast-alert .toast-alert-message {
        flex-grow: 1;
        padding: 0 8px;
    }

    .toast-alert .alert-success .indicator {
        background-color: green
    }

    .toast-alert .alert-error .indicator {
        background-color: red
    }
</style>
