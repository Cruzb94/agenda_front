<template>
    <div>
        <Header />
            <div class="container">
            <button type="button" class="btn btn-primary" v-on:click="openModalNuevo">Añadir Interes</button> <br><br>
            <h2>Usuarios Online</h2>
                <table class="table table-hover">
                    <thead>
                        <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Name</th>
                        <th scope="col">Email</th>
                        <th scope="col">Options</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="usuario in usuarios_on" :key="usuario.id">
                            <th scope="row">{{usuario.id}}</th>
                            <td>{{usuario.name}}</td>
                            <td>{{usuario.email}}</td>
                            <td>
                                <button type="button" style="color: white;" class="btn btn-info" v-on:click="openModalVer(usuario.id)">Ver Intereses</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <!-- Modal nuevo interes -->
                <modal name="nuevo_interes" >
                    <div class="container-fluid" style="height: 100px;">  
                        <br><br>
                        <form>
                            <div class="form-group">
                                <label for="exampleFormControlSelect1">Nuevo Interes</label>
                                
                                <select name="interes" class="form-control" v-on:change="onChange($event)">
                                    <option>Seleccione</option>
                                    <option v-for="interes in intereses" :key="interes.id" :value="interes.id">{{interes.name}}</option>
                                </select>
                            </div><br>
                            <button type="button" class="btn btn-primary" v-on:click="saveInterest">Guardar</button>
                        </form>
                    </div>    
                </modal>
                <!-- Modal ver intereses -->
                <modal name="ver_interes" >
                    <div class="container-fluid" style="height: 100px;">  
                        <br><br>
                        <form>
                            <div class="form-group">
                                <label for="exampleFormControlSelect1">Intereses</label>
                                
                                <ul class="list-group" v-for="interes in intereses_user_saved" :key="interes.id">
                                    <li class="list-group-item">{{interes.name}}</li>
                                </ul>
                            </div><br>
                        </form>
                    </div>    
                </modal>
            </div>
        <Footer />
    </div>
</template>
<script>
import Header from '@/components/Header.vue';
import Footer from '@/components/Footer.vue';

import axios from 'axios';

    export default {
        name: "Dashboard",
        data(){
            return {
                usuarios_on: '',
                intereses: '',
                interes_selected: '',
                intereses_user_saved: '',
            }
        },
        components: {
            Header,
            Footer,
        },
        mounted: function() {
            const config = {
                headers: { Authorization: `Bearer ${localStorage.token}` }
            };

            let url_users = 'http://127.0.0.1:8000/api/auth/users_online';

            axios.get(url_users, config).then(data => {
                this.usuarios_on = data.data;
                console.log(data);
            })

            let url_interests = 'http://127.0.0.1:8000/api/auth/interests';

            axios.get(url_interests, config).then(data => {
                this.intereses = data.data.interests;
                console.log('aqui', this.intereses[0].name);

                 
            })
        },
        methods: {
            openModalNuevo: function() {
                this.$modal.show('nuevo_interes');
            },
            openModalVer: function(id) {
                const headers = { 
                    "Authorization": `Bearer ${localStorage.token}`,
                };

                const url_save = 'http://127.0.0.1:8000/api/auth/interests-user';
                const interest = { user: id };

                axios.post(url_save, interest, { headers })
                    .then(data => {
                       this.intereses_user_saved = data.data; 
                     });

                this.$modal.show('ver_interes');

            },
            onChange: function(e){
                this.interes_selected = e.target.value;
                console.log('selected', this.interes_selected)
            },
            saveInterest: function() {
                const headers = { 
                    "Authorization": `Bearer ${localStorage.token}`,
                };

                const url_save = 'http://127.0.0.1:8000/api/auth/save-interests';

                const interest = { interes: this.interes_selected };
                axios.post(url_save, interest, { headers })
                    .then(response => 
                        console.log(response)
                    );

                    this.$modal.hide('nuevo_interes');
            },
        }   
    }

</script>
<style scoped>

</style>