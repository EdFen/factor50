<template>
  <div class="default-layout">
    <navbar :secondary="!!secondaryMenuSchema.length" />
    <side-nav :menu-schema="menuSchema" />
    <side-nav
      v-if="!!secondaryMenuSchema.length"
      :menu-schema="secondaryMenuSchema"
      type="secondary"
    />
    <main :class="{'menu-open': menuOpen, 'fixed-width': isMobile , 'secondary-menu-active': !!secondaryMenuSchema.length }">
      <slot name="breadcrumbs">
        <bread-crumbs base-crumb="dashboard" />
      </slot>
      <div class="main-slot">
        <slot />
      </div>
    </main>
  </div>
</template>

<script>
import SideNav from '@/components/nav/aside/Aside'
import BreadCrumbs from '@/components/nav/BreadCrumbs.vue'
import Navbar from '@/components/nav/Navbar'
import menuSchema from '@/helpers/schemas/menuSchema'

export default {
  name: 'DefaultLayout',
  components: {
    SideNav,
    BreadCrumbs,
    Navbar
  },
  props: {
    secondaryMenuSchema: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      menuSchema
    }
  },
  computed: {
    menuOpen () {
      return this.$store.state.menu.open
    },
    isMobile () {
      return this.$store.state.window.isMobile
    }
  }
}
</script>

<style lang="scss">
.default-layout {
  min-height: 100vh;
  background-color: #edf2f6;

  main {
    min-height: 100vh;
    transition: 250ms all ease-out;
    background-color: #edf2f6;
    width: 100%;
    padding-top: 3rem;
    margin-left: 24px; // safe 24px for toggle
    overflow: hidden;

    &.secondary-menu-active {
      margin-left: calc(224px + 24px); // safe 24px for toggle
      width: calc(100% - (224px + 24px));
    }

    &.menu-open {
      margin-left: 244px;
      width: calc(100% - 244px); // safe 24px for toggle

      &.secondary-menu-active {
        width: calc(100% - 224px * 2); // safe 24px for toggle
        margin-left: calc(224px * 2); // two menu's here!
      }
    }

    &.fixed-width {
      min-width: 100vw;
    }

    .form-section {
      padding-top: 0;
    }
  }
}
</style>
