<script setup>
import {ref, reactive, onMounted, watch}  from 'vue';
import {db} from './data/guitarras';
import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';


const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito, () => {
  guardarLocalStorage();
}, {
  deep: true,
})

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[7];

  const carritoStorage = localStorage.getItem('carrito')
  if(carritoStorage) {
    carrito.value = JSON.parse(carritoStorage)
  };

})

// Guardar en localstorage
const guardarLocalStorage = () => {
  localStorage.setItem('carrito', JSON.stringify(carrito.value));
}

const agregarCarrito = (guitarra) => {
  // Verficar si existe un producto en el carrito
  const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);
  if(existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
}

// Decrementar cantidad de productos en el carrito
const decrementarCanridad = (id) => {
  const index = carrito.value.findIndex(producto => producto.id === id);
  if(carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
}

// Incrementar cantidad de productos en el carrito
const incrementarCarrito = (id) => {
  const index = carrito.value.findIndex(producto => producto.id === id);
  if(carrito.value[index].cantidad >= 10) return;
  carrito.value[index].cantidad++;
}

// Eliminar productos de la carrito
const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter(producto => producto.id !== id)
}

// Vaciar productos del carrito
const vaciarCarrito = () => {
  carrito.value = [];
}

</script>

<template>
  <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad="decrementarCanridad"
    @incrementar-cantidad="incrementarCarrito"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
            <Guitarra 
              v-for="guitarra in guitarras" :key="guitarra"
              v-bind:guitarra="guitarra"
              @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>
  <Footer />
</template>

