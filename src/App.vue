<template>
    <main class="app-layout">

        <div class="app-body">
            <header class="app-header">
                <div class="app-header-content">
                    <h1>
                        Contacts
                    </h1>
                    <button @click="openCreateModal" class="app-btn">
                        Add new
                    </button>
                </div>
            </header>

            <div class="app-content">

                <ContactList :contacts="contactList" @delete="promptDelete" @edit="openEditModal">
                    <div class="ad-card" v-if="Object.keys(ad).length">
                        <h4 class="title">{{ad.company}}</h4>
                        <p class="description">{{ad.text}}</p>
                        <a :href="ad.url" target="_blank">Visit</a>
                    </div>
                </ContactList>

                <div style="text-align: center">
                    <Spinner size="40px" :loading="fetchingContacts"/>
                </div>
            </div>
        </div>

        <MicroModal :state="showCreateForm"
                    title="Add contact"
                    modalId="create-contact"
                    @close="dismissCreateModal">

            <ContactForm
              :formState="createFormState"
              @submit="createContact"/>

        </MicroModal>

        <MicroModal modalId="edit-modal" :state="showEditForm" @close="dismissEditModal">
            <ContactForm
              editMode
              :formState="editFormState"
              :formData="contactToEdit"
              @submit="editContact"/>
        </MicroModal>

        <MicroModal modalId="delete-modal" :state="showDeletePrompt" @close="dismissDeleteModal">
            <p>
                Are you sure you want to delete
                <strong>{{contactToDelete.first_name}}</strong>
                from your contact list?
            </p>
            <div class="delete-modal-actions">
                <button
                  @click="dismissDeleteModal"
                  :disabled="deleteState === 'DELETING'">
                    No
                </button>
                <button class="app-btn"
                        @click="deleteContact"
                        :disabled="deleteState === 'DELETING'">
                    <Spinner :loading="deleteState === 'DELETING'"/>
                    Yes
                </button>
            </div>
        </MicroModal>
        <ToastAlert :message="alert.message" :type="alert.type" @hide="resetAlert"/>

    </main>
</template>

<script>

import ContactList from './components/ContactList';
import MicroModal from './components/MircoModal';
import ContactForm from './components/ContactForm';
import Spinner from './components/Spinner';
import ToastAlert from './components/ToastAlert';

export default {
    name: 'App',
    components: {ToastAlert, Spinner, ContactForm, MicroModal, ContactList},
    data() {
        return {
            ad: {},
            contactList: [],
            fetchingContacts: false,
            showCreateForm: false,
            showEditForm: false,
            showDeletePrompt: false,
            scrollObserver: null,
            pageNumber: 1,
            contactToDelete: {},
            contactToDeleteIndex: null,
            contactToEdit: {},
            contactToEditIndex: null,
            deleteState: 'PENDING',
            createFormState: 'PENDING',
            editFormState: 'PENDING',
            alert: {
                type: '',
                message: ''
            }
        };
    },
    created() {
        this.getContacts();
    },
    methods: {
        incrementPage() {
            this.pageNumber += 1;
            this.getContacts();
        },
        openCreateModal() {
            this.showCreateForm = true;
        },
        dismissCreateModal() {
            this.showCreateForm = false;
        },
        openEditModal(index) {
            this.contactToEditIndex = index;
            this.contactToEdit = Object.assign({}, this.contactList[index]);
            this.showEditForm = true;
        },
        dismissEditModal() {
            this.showEditForm = false;
            this.contactToEdit = {};
            this.showEditForm = null;
        },
        promptDelete(index) {
            this.contactToDeleteIndex = index;
            this.contactToDelete = Object.assign({}, this.contactList[index]);
            this.showDeletePrompt = true;
        },
        dismissDeleteModal() {
            this.showDeletePrompt = false;
            this.contactToDeleteIndex = null;
            this.contactToDelete = {};
        },
        scrollCallback() {
            const heightDiff = document.documentElement.offsetHeight - (document.documentElement.scrollTop + window.innerHeight);
            if (heightDiff <= 20) {
                this.incrementPage();
            }
        },
        triggerAlert(type, message) {
            this.alert = {type, message}
        },
        resetAlert() {
            this.alert = {type: '', message: ''}
        },
        getContacts() {
            this.fetchingContacts = true;
            window.removeEventListener('scroll', this.scrollCallback);
            fetch(`https://reqres.in/api/users?page=${this.pageNumber}`)
                .then(data => data.json())
                .then(res => {
                    this.ad = res.ad || {};
                    this.contactList = this.contactList.concat(res.data);
                    if (res.data.length > 0) {
                        window.addEventListener('scroll', this.scrollCallback);
                    }
                })
                .catch(() => {
                    this.triggerAlert('error', "Something didn't go right, kindly try again.")
                })
                .finally(() => {
                    this.fetchingContacts = false;
                });
        },
        createContact(data) {
            this.createFormState = 'SUBMITTING';
            fetch(`https://reqres.in/api/users`, {
                method: 'POST',
                body: JSON.stringify(data)
            })
                .then(data => data.json())
                .then(res => {
                    this.contactList.splice(0, 0, {
                        id: res.id,
                        ...data
                    });
                    this.dismissCreateModal();
                    this.createFormState = 'SUCCESS';
                    this.triggerAlert('success', "You've successfully added a new contact")
                })
                .catch(() => {
                    this.createFormState = 'ERROR';
                    this.triggerAlert('error', "Something didn't go right, kindly try again.")
                });
        },
        editContact(data) {
            this.editFormState = 'SUBMITTING';
            fetch(`https://reqres.in/api/users`, {
                method: 'PATCH',
                body: JSON.stringify(data)
            })
                .then(data => data.json())
                .then(() => {
                    this.$set(this.contactList, this.contactToEditIndex, data);
                    this.dismissEditModal();
                    this.editFormState = 'SUCCESS';
                    this.triggerAlert('success', "You've successfully updated a contact")
                })
                .catch(() => {
                    this.editFormState = 'ERROR';
                    this.triggerAlert('error', "Something didn't go right, kindly try again.")
                });
        },
        deleteContact() {
            this.deleteState = 'DELETING';
            const contact = this.contactList[this.contactToDeleteIndex];
            fetch(`https://reqres.in/api/users/${contact.id}`, {
                method: 'DELETE'
            })
                .then(() => {
                    this.contactList.splice(this.contactToDeleteIndex, 1);
                    this.dismissDeleteModal();
                    this.deleteState = 'SUCCESS';
                    this.triggerAlert('success', "You've successfully deleted a contact")
                })
                .catch(() => {
                    this.deleteState = 'ERROR';
                    this.triggerAlert('error', "Something didn't go right, kindly try again.")
                });
        }
    }
};
</script>

<style>
    body {
        background-color: #f1f1f1;
    }

    .app-layout *, ::after, ::before {
        box-sizing: border-box;
    }

    .app-layout {
        font-family: 'Open Sans', sans-serif;
        max-width: 600px;
        margin-left: auto;
        margin-right: auto;
        padding: 0.75em;
        font-size: 14px;
        color: #222222;
    }

    .app-body {
        margin-top: 80px;
    }

    .app-header {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 80px;
        background-color: #FFFFFF;
    }

    .app-header-content {
        max-width: 600px;
        margin: auto;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0 0.75em;
    }

    .app-content {
        padding-top: 0.25em;
    }

    button {
        border-radius: 6px;
        height: 34px;
        padding: 0 1em;
        text-align: center;
        border: 1px solid #f5f5f5;
        background-color: transparent;
    }

    button * {
        vertical-align: middle;
    }

    .app-btn {
        color: #FFFFFF;
        background-color: #e53e3e;
    }

    button.link, a {
        border: none;
        text-decoration: none;
        outline: none;
        padding: 0;
        background-color: transparent;
    }

    button:focus {
        outline: none;
    }

    .delete-modal-actions {
        text-align: right;
    }

    .delete-modal-actions button {
        margin-left: 10px;
    }

    .ad-card {
        color: #FFFFFF;
        border-radius: 10px;
        text-align: center;
        background-color: #667eea;
        padding: 30px;
    }

    .ad-card .title {
        margin: 0;
    }

    .ad-card .description {
        font-size: 12px;
        margin: 8px 0 24px;
        font-weight: 200;
    }

    .ad-card a {
        color: white;
        border: 1px solid #FFFFFF;
        border-radius: 18px;
        padding: 0.5em 1em;
    }
</style>
