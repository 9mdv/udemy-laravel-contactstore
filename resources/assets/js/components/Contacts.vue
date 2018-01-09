<template>
    <div>
        <h1>Add Contacts</h1>
        <form action="#" @submit.prevent="edit ? updateContact(contact.id) : createContact()">
            <div class="form-group">
                <label>Name</label>
                <input type="text" name="name" class="form-control" v-model="contact.name">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="text" name="email" class="form-control" v-model="contact.email">
            </div>
            <div class="form-group">
                <label>Phone</label>
                <input type="text" name="phone" class="form-control" v-model="contact.phone">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-primary" v-show="!edit">New Contact</button>
                <button type="submit" class="btn btn-primary" v-show="edit">Update Contact</button>
            </div>
        </form>
        <h1>Contacts</h1>
        <ul class="list-group">
            <li class="list-group-item" v-for="contact in list">
                <strong>{{contact.name}}</strong> {{contact.email}} {{contact.phone}}
                <button @click="showContact(contact.id)" class="btn btn-default btn-xs">Edit</button>
                <button @click="deleteContact(contact.id)" class="btn btn-danger btn-xs">Delete</button>
            </li>
            <li class="list-group-item" v-if="!list.length">No contacts</li>
        </ul>
    </div>
</template>

<script>
    export default{
        data: function () {
            return {
                edit: false,
                list: [],
                contact: {
                    id: '',
                    name: '',
                    email: '',
                    phone: ''
                }
            }
        },
        mounted: function () {
            console.log('Contacts component loaded.');
            this.fetchContactList();
        },
        methods: {
            fetchContactList: function () {
                console.log('Fetching contacts...');
                axios.get('api/contacts')
                    .then((response) => {
                        console.log(response.data);
                        this.list = response.data;
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            },
            createContact: function () {
                console.log('Creating contact...');
                let self = this;
                let params = Object.assign({}, self.contact);
                axios.post('api/contact/store', params)
                    .then(function(){
                        self.contact.name = '';
                        self.contact.email = '';
                        self.contact.phone = '';
                        self.edit = false;
                        self.fetchContactList();
                        // console.log(params);
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
            },
            showContact: function (id) {
                let self = this;
                axios.get('api/contact/' + id)
                    .then(function (response) {
                        self.contact.id = response.data.id;
                        self.contact.name = response.data.name;
                        self.contact.email = response.data.email;
                        self.contact.phone = response.data.phone;
                    });
                    self.edit = true;
            },
            updateContact: function (id) {
                console.log('Updating contact...');
                let self = this;
                let params = Object.assign({}, self.contact);
                axios.patch('api/contact/' + id, params)
                    .then(function(){
                        self.contact.name = '';
                        self.contact.email = '';
                        self.contact.phone = '';
                        self.edit = false;
                        self.fetchContactList();
                        // console.log(params);
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
            },
            deleteContact: function (id) {
                let self = this;
                axios.delete('api/contact/' + id)
                    .then((response) => {
                        self.fetchContactList();
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            }
        }
    }
</script>