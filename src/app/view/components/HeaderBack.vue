<template>
  <q-header elevated class="primary-bg-color">
    <q-toolbar>
      <q-toolbar-title>
        <q-btn
          round
          flat
          icon="keyboard_arrow_left"
          color="white"
          stack
          :to="to"
        />
      </q-toolbar-title>
    </q-toolbar>
  </q-header>
</template>

<script>
export default {
  name: 'HeaderBack',
  props: {
    to: String
  }
}
</script>

<style scoped>

</style>
