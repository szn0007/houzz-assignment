<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated>
      <q-toolbar>

        <q-toolbar-title>
          Beer App
        </q-toolbar-title>

        <div>Houzz v1.0.0</div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'MainLayout',
  setup () {
    return {
    }
  }
})
</script>
