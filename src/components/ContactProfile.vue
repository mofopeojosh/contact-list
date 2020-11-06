<template>
    <div class="contact-profile">
        <ProfileAvatar :firstName="contact.first_name" :lastName="contact.last_name" :src="contact.avatar"/>
        <div class="contact-profile-body">
            <div class="contact-profile-details">
                <p class="contact-profile-name">{{contact.first_name}} {{contact.last_name}}</p>
                <p class="contact-profile-email">{{contact.email}}</p>
            </div>
            <!-- Actions go here -->
            <slot></slot>
        </div>
    </div>
</template>

<script>
import ProfileAvatar from './ProfileAvatar';

export default {
    name: 'ContactProfile',
    components: {ProfileAvatar},
    props: {
        contact: Object,
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .contact-profile {
        display: flex;
        align-items: center;
    }

    .contact-profile-body {
        flex-grow: 1;
        margin-left: 16px;
    }

    @media (min-width: 798px) {
        .contact-profile-body {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
    }

    .contact-profile-details {
        flex-grow: 1;
    }

    .contact-profile-details p {
        margin: 0;
    }

    .contact-profile-details .contact-profile-name {
        font-weight: 600;
        font-size: 18px;
    }

    .contact-profile-details .contact-profile-email {
        font-weight: 200;
        color: #777777;
    }

</style>
