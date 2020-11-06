<template>
    <div class="profile-avatar">
        <span class="profile-avatar-initials" v-if="showInitials">
            {{initials}}
        </span>
        <div v-else>
            <img class="profile-avatar-image" :src="src" @error="showInitials = true" :alt="initials">
        </div>
    </div>
</template>

<script>
export default {
    name: 'ProfileAvatar',
    props: {
        src: String,
        firstName: String,
        lastName: String,
    },
    data() {
        return {
            showInitials: false,
        };
    },
    computed: {
        initials() {
            const firstInitial = this.firstName[0] || '';
            const lastInitial = this.lastName[0] || '';
            return (firstInitial + lastInitial).toUpperCase();
        }
    },

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .profile-avatar {
        height: 84px;
        width: 84px;
        display: inline-block;
    }
    .profile-avatar-image, .profile-avatar-initials{
        border-radius: 50%;
        height: 100%;
        width: 100%;
        background-color: #F5F5F5;
        border: 1px solid #F5F5F5;
    }
    .profile-avatar-initials{
        display: inline-flex;
        align-items: center;
        justify-content: center;
        font-size: 24px;
    }
</style>
