<template>
  <v-content>
    <v-container>
      <TablaProductos />
    </v-container>
  </v-content>
</template>

<script>
import TablaProductos from "@/components/TablaProductos.vue"

export default {
  name: 'Productos',
  components: {
    TablaProductos
  }
}
</script>