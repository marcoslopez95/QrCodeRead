<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-primary">
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu"
        >
          <q-icon name="menu" />
        </q-btn>

        <q-toolbar-title>
          Quasar App
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      bordered
      content-class="bg-grey-2"
    >
      <q-list>
        <q-item>
          <q-img src="/qrLogo.jpg"></q-img>
        </q-item>
        <q-item-label header>Páginas</q-item-label>
        <q-item clickable to="/" exact>
          <q-item-section avatar>
            <q-icon name="camera_alt" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Lector código QR</q-item-label>
            <q-item-label caption></q-item-label>
          </q-item-section>
        </q-item>
        <q-item clickable to="generator" exact>
          <q-item-section avatar>
            <q-icon name="select_all" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Generador Código QR</q-item-label>
            <q-item-label caption></q-item-label>
          </q-item-section>
        </q-item>

      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'

export default {
  name: 'MyLayout',
  data () {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop
    }
  },
  methods: {
    openURL
  }
}
</script>

<style>
</style>
