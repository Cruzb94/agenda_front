<template>
    <div>
        <div class="container">
            <button type="button" id="new_contact" class="btn btn-primary" v-on:click="openModalNuevo">Añadir Contacto</button> <br><br>
            <h2>Contactos</h2>
                <table class="table table-hover">
                    <thead>
                        <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Nombre</th>
                        <th scope="col">Teléfono</th>
                        <th scope="col">Correo</th>
                        <th scope="col">Dirección</th>
                        <th scope="col">Options</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="contact in contacts" :key="contact.id">
                            <th scope="row">{{contact.id}}</th>
                            <td>{{contact.name}}</td>
                            <td>{{contact.phone}}</td>
                            <td>{{contact.email}}</td>
                            <td>{{contact.address}}</td>
                            <td>
                                <button type="button" style="color: white;" class="btn btn-info" v-on:click="openModalEdit(contact.id)">Editar</button>
                                <button type="button" class="btn btn-danger" style="margin-left: 10px;" v-on:click="deleteContact(contact.id)">Borrar</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <!-- Modal nuevo contacto -->
                <modal name="nuevo_contacto" style="height: 350px;">
                    <div class="container-fluid">  
                        <br>
                        <form>
                            <div class="form-group">
                                <label for="name">Nombre:</label>
                                <input type="text" class="form-control" id="name" placeholder="Nombre" v-model="name">
                            </div>
                            <div class="form-group">
                                <label for="email">Teléfono:</label>
                                <input type="text" class="form-control" id="email" placeholder="Teléfono" v-model="phone">
                            </div>
                            <div class="form-group">
                                <label for="email">email:</label>
                                <input type="text" class="form-control" id="email" placeholder="Email" v-model="email">
                            </div>
                            <div class="form-group">
                                <label for="description">Dirección:</label>
                                <input type="text" class="form-control" id="address" placeholder="Dirección" v-model="address">
                            </div>
                            <br>
                            <button type="button" class="btn btn-primary" style="margin-bottom: 60px;" v-on:click="saveContact">Guardar</button>
                        </form>
                    </div>    
                </modal>
                 <!-- Modal editar contacto -->
                <modal name="edit_contact" style="height: 350px;">
                    <div class="container-fluid">  
                        <br>
                        <form>
                            <div class="form-group">
                                <label for="name">Nombre:</label>
                                <input type="text" class="form-control" id="name" placeholder="Nombre" v-model="name">
                            </div>
                            <div class="form-group">
                                <label for="email">Teléfono:</label>
                                <input type="text" class="form-control" id="email" placeholder="Teléfono" v-model="phone">
                            </div>
                            <div class="form-group">
                                <label for="email">email:</label>
                                <input type="text" class="form-control" id="email" placeholder="Email" v-model="email">
                            </div>
                            <div class="form-group">
                                <label for="description">Dirección:</label>
                                <input type="text" class="form-control" id="address" placeholder="Dirección" v-model="address">
                            </div>
                            <br>
                            <button type="button" class="btn btn-primary" style="margin-bottom: 60px;" v-on:click="updateContact">Guardar</button>
                        </form>
                    </div>    
                </modal>
            </div>
        <Footer />
    </div>
</template>
<script>

import axios from 'axios';

    export default {
        name: "Dashboard",
        data(){
            return {
                id: '',
                name: '',
                email: '',
                phone: '',
                address: '',
                contacts: [],
            }
        },
        components: {

        },
        mounted: function() {
         this.getContacts();
        },
        methods: {
            getContacts() {
                const config = {
                headers: { Authorization: `Bearer ${localStorage.token}` }
            };
                let url_groups = process.env.VUE_APP_URL+'/api/auth/contacts';
                axios.get(url_groups, config).then(data => {
                    this.contacts = data.data.contacts;
                    console.log('getContacts',this.contacts);  
                })
            },
            deleteContact: function(id) {
                let result = window.confirm('¿Desea eliminar este contacto?');
  
                if(result) {
                    const config = {
                        headers: { Authorization: `Bearer ${localStorage.token}` }
                    };

                    const url = process.env.VUE_APP_URL+'/api/auth/delete';

                    let formData = new FormData();
                    formData.append('id', id);

                    axios.post(url, formData, config)
                        .then(data => {

                            alert(data.data.message);
                            this.getContacts();
                           
                    });
                }
            },
            openModalNuevo: function() {
                this.$modal.show('nuevo_contacto');
            },
            openModalEdit: function(id) {
                for (let i = 0; i < this.contacts.length; i++) {
                   if(this.contacts[i].id == id) {
                        this.name = this.contacts[i].name;
                        this.phone = this.contacts[i].phone;
                        this.email = this.contacts[i].email;
                        this.address = this.contacts[i].address;
                        this.id = id;
                   }  
                }
                this.$modal.show('edit_contact');

            },
            onChange: function(e){
                this.interes_selected = e.target.value;
                console.log('selected', this.interes_selected)
            },
            saveContact: function() {
                const config = {
                    headers: { Authorization: `Bearer ${localStorage.token}` }
                };
                const url = process.env.VUE_APP_URL+'/api/auth/save-contact';

                let formData = new FormData();

                formData.append('name', this.name);
                formData.append('email', this.email);
                formData.append('phone', this.phone);
                formData.append('address', this.address);

                axios.post(url, formData, config)
                    .then(data => {

                        alert(data.data.message);
                        this.getContacts();
                        this.name = '';
                        this.phone = '';
                        this.email = '';
                        this.address = '';
                        this.$modal.hide('nuevo_contacto');
                });
            },
             updateContact: function() {
                const config = {
                    headers: { Authorization: `Bearer ${localStorage.token}` }
                };
                const url = process.env.VUE_APP_URL+'/api/auth/update-contact';

                let formData = new FormData();

                formData.append('id', this.id);
                formData.append('name', this.name);
                formData.append('email', this.email);
                formData.append('phone', this.phone);
                formData.append('address', this.address);

                axios.post(url, formData, config)
                    .then(data => {

                        alert(data.data.message);
                        this.getContacts();
                        this.id = '';
                        this.name = '';
                        this.phone = '';
                        this.email = '';
                        this.address = '';
                        this.$modal.hide('edit_contact');
                });
            },
        }   
    }

</script>
<style>

.vm--modal {
    height: 350px !important
}

.container {
    margin-top: 50px;
}

#new_contact {
    display: flex;
}
</style>