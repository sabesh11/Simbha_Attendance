<template>
<div class="container q-pa-lg bg-grey-1">
    <div class="row justify-evenly bg-white q-pa-md q-mt-md q-border-radius-md q-shadow-2">
        <div class="col-md-5 self-center desktop-only">
            <q-img src="https://img.freepik.com/free-vector/sign-page-abstract-concept-illustration_335657-3875.jpg" alt="" :ratio="1" />
        </div>
        <div class="col-md-4 col-12 self-center">
            <div class="text-h4 text-indigo-14 text-weight-bold text-center q-mt-sm">Attendance</div>
            <div class="text-center mobile-only q-mt-sm">
                <img src="https://img.freepik.com/free-vector/sign-page-abstract-concept-illustration_335657-3875.jpg" alt="" height="150px" width="150px" />
            </div>
            <div class="q-mt-lg">
                <q-input filled v-model="username" label="Username" />
            </div>
            <div class="q-mt-lg">
                <q-input v-model="password" label="Password" filled :type="isPwd ? 'password' : 'text'" hint="Password with toggle">
                    <template v-slot:append>
                        <q-icon :name="isPwd ? 'visibility_off' : 'visibility'" class="cursor-pointer" @click="isPwd = !isPwd" />
                    </template>
                </q-input>
            </div>
            <div class="q-mt-lg row justify-center">
                <div class="col-md-5 col-6 q-mt-sm">
                    <a href="" class="text-indigo-14" style="text-decoration: none;">Forgot password?</a>
                </div>
            </div>
            <div class="q-mt-lg">
                <q-btn class="full-width bg-indigo-14 text-white" color="" label="Sign In" @click="addEmployee" flat/>
            </div>
            <div class="q-mt-lg text-center">
                Don't have an account? <a href="" class="text-indigo-14">signup</a>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'IndexPage',
    data() {
        return {

            username: null,
            password: null,
            isPwd: true,
        };
    },
    methods: {
        async addEmployee() {
            if (!this.username || !this.password) {
                this.$q.notify({
                    type: 'negative',
                    message: 'Please fill in both fields',
                });
                return;
            }

            const newEmployee = {
                username: this.username,
                password: this.password,
            };

            axios.post('http://localhost:3000/emplyoee/addEmployee', newEmployee)
            .then(res => {

                    this.$q.notify({
                        type: 'positive',
                        message: 'login successfully',
                        position:'top'
                    });

                    console.log('Employee created:', res.data);
                    localStorage.setItem("username", res.data.username)
                    localStorage.setItem("userId",res.data._id)
                    this.$router.push('/Attendance');
                })
                .catch(error => {
                    this.$q.notify({
                        type: 'negative',
                        message: 'There was an error in login: ' + error.message,
                        position:'top'
                    });
                    console.error('There was an error creating the employee:', error.message);
                })
        }
    }
};
</script>

<style scoped>
.q-border-radius-md {
    border-radius: 8px;
}

.q-shadow-2 {
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
