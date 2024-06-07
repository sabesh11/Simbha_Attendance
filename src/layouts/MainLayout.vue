<template>
<div class="">
    <q-layout view="lHh Lpr lff">
        <q-header flat :class="$q.dark.isActive ? 'bg-secondary' : 'bg-indigo-14'" class="lt-md">
            <q-toolbar>
                <q-btn flat @click="drawer = !drawer" round dense icon="menu" class="gt-sm" />
                <q-toolbar-title>Attendance </q-toolbar-title>
                <q-avatar size="28px" class="bg-white text-indigo-14">{{ firstLetter }}
                    <q-menu>
                        <div class="row no-wrap q-pa-md bg-grey-3">

                            <div class="column items-center">
                                <q-avatar size="30px">
                                    <img src="https://cdn.quasar.dev/img/avatar4.jpg">
                                </q-avatar>

                                <div class="text-weight-bold q-mt-md q-mb-xs">{{ username }}</div>
                                <div class="q-mb-xs">Employee</div>

                                <q-btn class="bg-indigo-14 text-white" label="Logout" flat size="sm" v-close-popup @click="logout()" />
                            </div>
                        </div>
                    </q-menu>
                </q-avatar>

            </q-toolbar>
        </q-header>

        <q-page-container>
            <RouterView />

        </q-page-container>
    </q-layout>
</div>
</template>

<script>
import {
    ref
} from 'vue'

export default {
    setup() {

        return {

            username: '',
            firstLetter: '',

        }
    },
    mounted() {
        this.username = localStorage.getItem("username")
        this.firstLetter = this.username.charAt(0)
        console.log(this.firstLetter);
    },
    methods: {
        logout() {
            this.$router.push('/')
        }
    }

}
</script>

<style scoped>

</style>
