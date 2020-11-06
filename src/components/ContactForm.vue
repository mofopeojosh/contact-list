<template>
    <form name="contact-form" @submit.prevent="validateContactForm">
        <h3 class="form-title">{{ editMode ? 'Edit' : 'New' }} Contact</h3>
        <div class="form-input">
            <label for="contact-first-name">First Name</label>
            <input type="text" name="first_name" id="contact-first-name" v-model="contactForm.first_name">
            <span class="form-input-error">{{errors.first_name}}</span>
        </div>
        <div class="form-input">
            <label for="contact-last-name">Last Name</label>
            <input type="text" name="email" id="contact-last-name" v-model="contactForm.last_name">
            <span class="form-input-error">{{errors.last_name}}</span>
        </div>
        <div class="form-input">
            <label for="contact-email">Email</label>
            <input type="text" name="last_name" id="contact-email" v-model="contactForm.email">
            <span class="form-input-error">{{errors.email}}</span>
        </div>
        <div class="form-input">
            <label for="file-input">Avatar</label>
            <div class="file-input">
                <input name="email" id="contact-avatar" v-model="fileName" type="text" autocomplete="off" readonly>
                <span role="button"
                      tabindex="0"
                      aria-controls="file-input"
                      class="file-input-trigger"
                      @click.prevent="triggerFileInput">
                    {{uploadingFile ? 'Uploading...' : 'Choose'}}
                </span>
            </div>
            <input class="form-input-hidden" ref="fileInput" type="file" @change="handleFileUpload" id="file-input">
            <span class="form-input-error">{{errors.avatar}}</span>
        </div>

        <div class="form-action">
            <button class="app-btn"
                    type="submit"
                    :disabled="formState === 'SUBMITTING' || uploadingFile">
                <Spinner :loading="formState === 'SUBMITTING'"/>
                {{ editMode ? 'Update' : 'Add' }} contact
            </button>
        </div>
    </form>


</template>

<script>

import Spinner from './Spinner';

export default {
    name: 'ContactForm',
    components: {Spinner},
    props: {
        formState: String,
        editMode: Boolean,
        formData: Object
    },
    data() {
        return {
            contactForm: {
                first_name: '',
                last_name: '',
                email: '',
                avatar: ''
            },
            errors: {},
            fileName: '',
            uploadingFile: false,
        };
    },
    watch: {
        formData(newVal) {
            if (this.formState === 'SUCCESS' || this.formState === 'PENDING') {
                this.contactForm = newVal;
                this.fileName = newVal.avatar;
            }
        },
        formState() {
            this.resetForm();
        }
    },
    mounted() {
        if (this.editMode) {
            this.contactForm = this.formData;
        }
    },
    methods: {
        resetForm() {
            this.contactForm = {
                first_name: '',
                last_name: '',
                email: '',
                avatar: ''
            };
            this.fileName = '';
        },
        validateContactForm() {
            this.errors = {};
            let noErrors = true;
            const {first_name, last_name, email, avatar} = this.contactForm;

            if (first_name.length === 0) {
                noErrors = false;
                this.errors.first_name = 'Kindly add a first name';
            }
            if (last_name.length === 0) {
                noErrors = false;
                this.errors.last_name = 'Kindly add a last name';
            }
            if (email.length === 0) {
                noErrors = false;
                this.errors.email = 'Kindly add an email';
            }
            if (avatar.length === 0) {
                noErrors = false;
                this.errors.avatar = 'Kindly add an avatar';
            }

            if (noErrors) {
                this.$emit('submit', this.contactForm);
            }
        },
        triggerFileInput() {
            this.$refs.fileInput.click();
        },
        handleFileUpload(event) {
            this.uploadingFile = true;
            const file = event.target.files[0];
            this.fileName = file.name;
            const formData = new FormData();
            formData.append('file', file);
            formData.append('expires', '1h');
            fetch('https://api.anonymousfiles.io', {
                method: 'POST',
                body: formData
            })
                .then(data => data.json())
                .then((res) => {
                    this.contactForm.avatar = `https://anonymousfiles.io/f/${res.name}`;
                })
                .catch(() => {
                    this.errors.avatar = 'Kindly try uploading again';
                })
                .finally(() => {
                    this.uploadingFile = false;
                });
        },
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    .form-title {
        margin-top: 0;
    }

    .form-input {
        margin-bottom: 16px;
        min-width: 350px;
    }

    .form-input label {
        color: #333333;
        font-weight: normal;
        text-transform: uppercase;
        font-size: 11px;
        display: inline-block;
        margin-bottom: 4px;
    }

    .form-input input, .file-input {
        color: #333333;
        width: 100%;
        border-radius: 5px;
        height: 36px;
        padding: 0 0.5em;
        box-shadow: none;
        border: 1px solid transparent;
        background-color: #d5d9d9;
    }

    .form-input input:focus {
        outline: none;
    }

    .form-input-hidden {
        display: none;
    }

    .file-input {
        display: flex;
        align-items: center;
        padding-right: 0;
        justify-content: space-between;
    }

    .file-input input {
        border: none;
    }

    .file-input-trigger {
        display: inline-flex;
        align-items: center;
        height: 100%;
        font-size: 12px;
        padding: 0 12px;
        border-radius: inherit;
        border: 1px dotted #000000;
    }

    .file-input-trigger:hover {
        cursor: pointer;
    }

    .file-input-trigger:focus {
        outline: none;
    }

    .form-input-error {
        font-size: 11px;
        font-weight: 200;
        color: #696969;
    }

    .form-action {
        margin-top: 30px;
        text-align: right;
    }

</style>
