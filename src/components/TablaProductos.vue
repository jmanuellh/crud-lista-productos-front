<template lang="pug">
  div
    v-data-table(
      :headers="cabeceras",
      :items="productos"
    )
      template( v-slot:top )
        v-toolbar( flat )
          v-toolbar-title Productos
          v-spacer
          v-btn( @click="dialogProducto = true" color="primary" ) Nuevo Producto
      template( v-slot:item.action="{ item }" )
        v-icon( @click="editarProducto(item.id)" ) fas fa-edit
        v-icon( @click="confirmarEliminarProducto(item.id)" ) fas fa-trash
      template(v-slot:no-data)
        v-btn( color="primary" @click="obtenerProductos" :disabled="disabledReintentar") Reintentar

    v-dialog( v-model="dialogProducto" )
      v-card
        v-card-title( primary-title ) Producto
        v-card-text
          v-container
            v-row
              v-col
                v-text-field(
                  v-model="productoAGuardar.nombre"
                  label="Nombre"
                )
              v-col
                v-text-field(
                  type="number"
                  step=".5"
                  v-model="productoAGuardar.precio"
                  label="Precio"
                )
        v-card-actions
          v-spacer
          v-btn( @click="cerrarDialogProducto" ) Cancelar
          v-btn( @click="guardarProducto" ) Guardar

    v-dialog( v-model="dialogEliminarProducto" )
      v-card
        v-card-title( primary-title ) Eliminar
        v-card-text ¿Seguro que desea eliminar el producto?
        v-divider
        v-card-actions
          v-spacer
          v-btn( @click="dialogEliminarProducto = false" ) Cancelar
          v-btn( @click="eliminarProducto()" ) Eliminar
</template>>

<script>
import ProductosVue from '../views/Productos.vue';
export default {
  name: 'TablaProductos',
  data() {
    return {
      cabeceras: [
        { text: 'Nombre', value: 'nombre' },
        { text: 'Precio', value: 'precio' },
        { text: 'Acciones', value: 'action' }
      ],
      dialogEliminarProducto: false,
      dialogProducto: false,
      disabledReintentar: false,
      urlProductos: 'https://localhost:5001/api/productos/',
      productos: [],
      productoAGuardar: {},
      productoAEliminar: {}
    }
  },
  mounted() {
    this.obtenerProductos();
  },
  methods: {
    obtenerProductos() {
      this.disabledReintentar = true
      fetch(this.urlProductos)
      .then(res => res.json())
      .catch(error => {})
      .then(response => {
        this.disabledReintentar = false
        this.productos = response
      })
    },
    confirmarEliminarProducto(productoId) {
      this.dialogEliminarProducto = true
      this.productoAEliminar.id = productoId
    },
    eliminarProducto() {
      fetch(this.urlProductos+this.productoAEliminar.id, {
        method: 'DELETE'
      })
      .then(res => res.json())
      .catch(error => {})
      .then(response => {
        this.obtenerProductos()
        this.dialogEliminarProducto = false
        this.productoAEliminar = {}
      })
    },
    editarProducto(productoId) {
      fetch(this.urlProductos+productoId)
      .then(res => res.json())
      .catch(error => {})
      .then(response => {
        this.productoAGuardar = response
      })
      this.dialogProducto = true
    },
    cerrarDialogProducto() {
      this.dialogProducto = false
      this.productoAGuardar = {}
    },
    guardarProducto() {
      if(this.productoAGuardar.id) {
        // Actualizar producto
        this.productoAGuardar.precio = parseFloat(this.productoAGuardar.precio)
        fetch(this.urlProductos+this.productoAGuardar.id, {
          method: 'PUT',
          body: JSON.stringify(this.productoAGuardar),
          headers: { 'Content-Type': 'application/json' }
        })
        .then(res => res.json())
        .catch(error => {})
        .then(response => {
          this.obtenerProductos()
          this.cerrarDialogProducto()
        })
      } else {
        // Nuevo producto
        fetch(this.urlProductos, {
          method: 'POST',
          body: JSON.stringify({
            nombre: this.productoAGuardar.nombre,
            precio: parseFloat(this.productoAGuardar.precio)
          }),
          headers:{ 'Content-Type': 'application/json' }
        })
        .then(res => res.json())
        .catch(error => {})
        .then(() => {
          this.obtenerProductos()
          this.cerrarDialogProducto()
        })
      }

    }
  }
}
</script>