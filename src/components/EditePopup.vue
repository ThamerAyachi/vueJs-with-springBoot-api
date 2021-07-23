<template>
    <v-row class="addrow">
        <!-- Error Snackbar -->
        <v-snackbar v-model="UeSnackbar" color="error" top :timeout="4000">
            <span>Error! Email Taken. Try other Email.</span>
            <v-btn text  color="white" @click="UeSnackbar=false" class="ml-4"><v-icon>mdi-window-close</v-icon></v-btn>
        </v-snackbar>

        <!-- success Snackbar -->
        <v-snackbar v-model="UsSnackbar" color="success" top :timeout="4000">
            <span>Awesome! You Update a employee. </span>
            <v-btn text  color="white" @click="UsSnackbar=false" class="ml-4"><v-icon>mdi-window-close</v-icon></v-btn>
        </v-snackbar>

        <v-dialog
            v-model="dialog"
            max-width="600px"
        >
            <!-- Edite Button -->
            <template v-slot:activator="{ on, attrs}">
                <v-btn color="blue" 
                text small
                v-bind="attrs"
                v-on="on" 
                class="mx-7"
                @click="open(firstName, lastName, email, salary, id)"
                >
                    <v-icon>mdi-account-edit</v-icon>
                </v-btn>
            </template>

            <!-- Card -->
            <v-card>
                <v-card-title primary-title>
                    <span class="headline">Edite Employee</span>
                </v-card-title>

                <v-card-text>
                    <!-- Form -->
                    <v-form class="px-3" ref="form">
                        <v-text-field
                            name="firstName"
                            label="First Name"
                            :rules="inputRules"
                            v-model="FirstName"
                        ></v-text-field>

                        <v-text-field
                            name="lastName"
                            label="Last Name"
                            :rules="inputRules"
                            v-model="LastName"
                        ></v-text-field>

                        <v-text-field
                            name="email"
                            label="Email"
                            :rules="inputRules"
                            v-model="Email"
                        ></v-text-field>

                        <v-text-field
                            name="salary"
                            label="Salary"
                            v-model="Salary"
                        ></v-text-field>

                        <!-- Save Button -->
                        <v-btn dark class="blue mx-0 mt-3" 
                        @click="putData()" 
                        :loading="loading" >Edite employee</v-btn>
                        <!-- Close Button -->
                        <v-btn text color="grey" @click="dialog=false" class="mt-3">Close</v-btn>
                    </v-form>
                </v-card-text>
            </v-card>
        </v-dialog>
    </v-row>
</template>

<script>
import axios from 'axios'   

export default {
    name: "EditePopup",
    props: [
        "firstName",
        "lastName",
        "email",
        "salary",
        "id"
    ],
    data() {
        return {
            dialog: null,
            FirstName: '',
            LastName: '',
            Email: '',
            Salary: '',
            Id: '',
            inputRules: [
                v => !!v || 'That is required',
                v => v.length >= 3 || 'Minimum length is 3 characters',
            ],
            loading: false,
            UeSnackbar: false,
            UsSnackbar : false,
        }
    },
    methods: {
        open(firstName, lastName, email, salary, id){
            // Declaration 
            this.FirstName = firstName;
            this.LastName = lastName;
            this.Email = email;
            this.Salary = Number(salary);
            this.Id = Number(id);
        },
        putData(){

            // Put
            if(this.$refs.form.validate()){
                this.loading = true;

                axios.put(`http://localhost:8080/api/v1/employe/update/${this.Id}?firstName=${this.FirstName}&\
                lastName=${this.LastName}&email=${this.Email}&salary=${this.Salary}`).then(() => {

                    // Close
                    this.loading = false;
                    this.dialog = false;

                    // Event 
                    this.$emit('employeeUpdate')
                    this.UsSnackbar = true;
                }).catch(() => {
                    this.loading = false;
                    this.UeSnackbar = true;
                    this.Email = "";
                })          
            }



        },
    },
}
</script>