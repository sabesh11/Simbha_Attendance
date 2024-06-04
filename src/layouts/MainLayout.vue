<template>
  <div class="">
    <q-layout view="lHh Lpr lff">
      <q-header flat  :class="$q.dark.isActive ? 'bg-secondary' : 'bg-indigo-14'" class="lt-md">
        <q-toolbar>
          <q-btn flat @click="drawer = !drawer" round dense icon="menu" />
          <q-toolbar-title>Attendance </q-toolbar-title>
          <q-avatar size="28px"  class="bg-white text-indigo-14">{{ firstLetter }}</q-avatar>

        </q-toolbar>
      </q-header>

      <q-drawer
        v-model="drawer1"
        show-if-above

        :mini="!drawer1 || miniState"
        @click.capture="drawerClick"

        :width="230"
        :breakpoint="500"
        class="gt-sm"
        :class="$q.dark.isActive ? 'bg-grey-9' : ''"
      >
        <q-scroll-area class="fit fixed q-mini-drawer-hide bg-grey-1" :horizontal-thumb-style="{ opacity: 0 }" style=" margin-top: 150px;">
          <q-list padding>
            <q-item clickable v-ripple active>
              <q-item-section avatar>
                <q-icon name="event" />
              </q-item-section>

              <q-item-section>
                Attendance
              </q-item-section>
            </q-item>



           

           
          </q-list>
        </q-scroll-area>
        <q-img class="  absolute-top q-mini-drawer-hide" src="https://cdn.quasar.dev/img/material.png" style="height: 150px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="https://cdn.quasar.dev/img/boy-avatar.png">
            </q-avatar>
            <div class="text-weight-bold">{{ username }}</div>
            <div>Employee</div>
          </div>
        </q-img>
        <!--
          in this case, we use a button (can be anything)
          so that user can switch back
          to mini-mode
        -->
        <div class="q-mini-drawer-active absolute" :style="{ top: '255px', right: miniState ? '24px' : '-29px' }">
          <q-btn
            dense
            class="btn"
            unelevated
            color="indigo-14"
            icon="chevron_left"
            @click="miniState = true"
          />
        </div>
      </q-drawer>
      <q-page-container>
        <RouterView/>
        
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup () {
    const miniState = ref(false)
    return {
      drawer1: ref(false),
      miniState,
      username:'',
      firstLetter:'',
      
      
      drawerClick (e) {
        // if in "mini" state and user
        // click on drawer, we switch it to "normal" mode
        if (miniState.value) {
          miniState.value = false

          // notice we have registered an event with capture flag;
          // we need to stop further propagation as this click is
          // intended for switching drawer to "normal" mode only
          e.stopPropagation()
        }
      }
    }
  },
  mounted(){
   this.username=localStorage.getItem("username")
   this.firstLetter = this.username.charAt(0)
   console.log(this.firstLetter);
  },
  
}
</script>
<style scoped>
.btn{
border-top-right-radius: 50%;
border-bottom-right-radius: 50%;
padding: 10px;
}


</style>
