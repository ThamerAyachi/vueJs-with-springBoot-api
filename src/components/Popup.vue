<template>
    <v-row class="addrow">
        <!-- Snackbar -->
        <v-snackbar v-model="eSnackbar" color="error" top :timeout="4000">
            <span>Error! Email Taken. Try other Email.</span>
            <v-btn text  color="white" @click="eSnackbar=false" class="ml-4"><v-icon>mdi-window-close</v-icon></v-btn>
        </v-snackbar>
        <v-dialog
            v-model="dialog"
            persistent
            max-width="600px"
        >
            <!-- Added Button -->
            <template v-slot:activator="{ on, attrs }">
                <v-btn 
                color="blue" 
                v-bind="attrs" 
                v-on="on" 
                elevation="0" 
                tile small text 
                class="mb-7 mx-3">
                    <v-icon small left>mdi-account-plus</v-icon>
                    <span class="caption text-lowercase">Add new Employee</span>
                </v-btn>
            </template>

            <v-card>
                <v-card-title >
                    <span class="headline">Add new Employee</span>
                </v-card-title>

                <v-card-text>
                    <!-- Form -->
                    <v-form class="px-3" ref="form" >
                        <v-text-field
                            name="firstName"
                            label="First Name"
                            :rules="inputRules"
                            v-model="firstName"
                        ></v-text-field>

                        <v-text-field
                            name="lastName"
                            label="Last Name"
                            :rules="inputRules"
                            v-model="lastName"
                        ></v-text-field>

                        <v-text-field
                            name="email"
                            label="Email"
                            :rules="inputRules"
                            v-model="email"
                        ></v-text-field>

                        <v-text-field
                            name="salary"
                            label="Salary"
                            :rules="inputRules"
                            v-model="salary"
                        ></v-text-field>

                        <!-- Date -->
                        <v-menu v-model="menu2" :close-on-content-click="false" :nudge-right="40"
                        transition="scale-transition" offset-y min-width="auto"
                        >
                        <template v-slot:activator="{ on, attrs }">
                            <v-text-field readonly v-on="on" v-bind="attrs" 
                                v-model="due" label="Due date" prepend-icon="mdi-calendar-range"
                                :rules="inputRules"
                            ></v-text-field>
                        </template>
                        <v-date-picker  @input="menu2 = false" v-model="due"></v-date-picker>
                        </v-menu>
                        <!-- /date -->



                        <!-- Save Button -->
                        <v-btn dark class="blue mx-0 mt-3" @click="postData()" :loading="loading" >Add employee</v-btn>
                        <!-- Close Button -->
                        <v-btn text color="grey" @click="close()" class="mt-3">Close</v-btn>
                    </v-form>
                </v-card-text>
            </v-card>
        </v-dialog>
    </v-row>
</template>

<script>
import axios from 'axios'

    export default {
        name : "Popup",
        data() {
            return {
                dialog: null,
                firstName: '',
                lastName: '',
                email: '',
                salary: '',
                due : new Date().toISOString().substr(0, 10),
                menu: false,
                modal: false,
                menu2: false,
                inputRules: [
                    v => !!v || 'That is required',
                    v => v.length >= 3 || 'Minimum length is 3 characters',
                ],
                data: null,
                loading: false,
                eSnackbar : false,
            }
        },
        methods: {
            postData(){
                this.data = {
                    firstName : this.firstName,
                    lastName : this.lastName,
                    email : this.email,
                    salary: Number(this.salary),
                    dob : this.due
                }
                if(this.$refs.form.validate()){
                    this.loading = true;
                    axios.post('http://localhost:8080/api/v1/employe', this.data).then(() => {

                        // Clear
                        this.firstName = '';
                        this.lastName = '';
                        this.email = '';
                        this.salary = '';

                        // Close
                        this.loading = false;
                        this.dialog = false;

                        // Sneckbar
                        this.$emit('employeeAdded')


                        

                    }).catch(() =>{
                        this.loading = false;
                        this.eSnackbar = true;
                        this.email = "";

                    })
                }

            },
            close(){
                // Clear
                this.firstName = '';
                this.lastName = '';
                this.email = '';
                this.salary = '';
                this.dialog = false
            }
            
        },
    }
</script>

<style lang="scss" scoped>

</style>