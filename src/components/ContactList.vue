<template>
    <ul class="contact-list">
        <li v-for="(contact, index) in contacts" :key="index">
            <template v-if="index === 3">
                <!--Ad goes here-->
                <slot></slot>
            </template>

            <div class="contact-card">
                <ContactProfile :contact="contact">
                    <div class="contact-card-actions">
                        <a href="#" class="link" @click.prevent="editContact(index)">edit</a>
                        <a href="#" class="link" @click.prevent="deleteContact(index)">delete</a>
                    </div>
                </ContactProfile>
            </div>
        </li>
        <li v-if="contacts.length <= 3">
            <!--Ad goes here-->
            <slot></slot>
        </li>
    </ul>

</template>

<script>
import ContactProfile from './ContactProfile';

export default {
    name: 'ContactList',
    components: {ContactProfile},
    props: {
        contacts: Array
    },
    methods: {
        editContact(index) {
            this.$emit('edit', index);
        },
        deleteContact(index) {
            this.$emit('delete', index);
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .contact-list {
        list-style: none;
        padding: 0 0;
    }

    .contact-card {
        padding: 1.25em 1em;
        margin: 30px auto;
        border-radius: 10px;
        background-color: #FFFFFF;
        box-shadow: 0 1px 10px 1px rgba(0, 0, 0, .1);
    }

    .contact-card-actions {
        margin-top: 5px;
        font-weight: 200;
    }

    .contact-card-actions a {
        padding-right: 5px;
        color: #e53e3e;
    }
</style>
