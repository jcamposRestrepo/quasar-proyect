<template>
  <div class="q-pa-md">
    <div class="q-gutter-y-md column" style="max-width: 300px">
      <q-input rounded outlined v-model="email" type="email" suffix="@gmail.com">
        <template v-slot:before>
          <q-icon name="mail" />
        </template>
      </q-input>

      <q-input rounded outlined v-model="password" label="Contraseña" type="password" >
      </q-input>

      <div class="q-pa-md q-gutter-sm">
        <q-btn color="secondary" label="Secondary" @click="login" />
      </div>

    </div>
  </div>
</template>

<script>
import {ref} from "vue";
import { createWebHistory, createRouter } from "vue-router";
import router from "src/router";


export default {
  setup(){
    const email = ref("")
    const password = ref("")

    return {email,password}
  },
  methods:{
    async login(){
      console.log(this.email)
      console.log(this.password)
      router.push('/init');
    }
  }
}
</script>
