<script setup>
import { ref, onMounted, watch } from 'vue'
import { db } from './data/guitarras'
import Header from './components/Header.vue';
import Guitarra from './components/Guitarra.vue'
import Footer from './components/Footer.vue'

const guitarras = ref([])
const carrito = ref([])
const guitarraHeader = ref({})

watch(carrito, ()=>{
  guardarLS()
}, {
  deep: true
})

onMounted(()=>{
  guitarras.value = db
  guitarraHeader.value = db[3]
  leerLS()
})


const guardarLS =()=>{
  localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const leerLS =()=>{
  if(localStorage.getItem('carrito')){
    carrito.value = JSON.parse(localStorage.getItem('carrito'))
  }
}

const agregarCarrito =(guitarra)=>{
  const existeGuitarra = carrito.value.findIndex(item => item.id === guitarra.id)
  if (existeGuitarra >=0){
    carrito.value[existeGuitarra].cantidad++
    return
  }
  guitarra.cantidad = 1 
  carrito.value.push(guitarra)
}

const decrementarCantidad =(id)=>{
  const indice = carrito.value.findIndex(item => item.id === id)
  if(carrito.value[indice].cantidad === 1) return 
  carrito.value[indice].cantidad--
}

const incrementarCantidad =(id)=>{
  const indice = carrito.value.findIndex(item => item.id === id)
  if(carrito.value[indice].cantidad >= 6) return
  carrito.value[indice].cantidad++ 
}

const eliminarProducto=(id)=>{
  carrito.value = carrito.value.filter(item => item.id !== id)
}

const vaciarCarrito = ()=>{
  carrito.value = []
}

</script>

<template>
 <Header 
  :carrito="carrito"
  :guitarra="guitarraHeader"
  @incrementar-cantidad="incrementarCantidad"
  @decrementar-cantidad="decrementarCantidad"
  @eliminar-producto="eliminarProducto"
  @vaciar-carrito="vaciarCarrito"
  @agregar-carrito="agregarCarrito"
 />
  <main class="container-xl mt-5">
      <h2 class="text-center">Nuestra Colección</h2>

      <div class="row mt-5">
          <Guitarra 
              v-for="guitarra in guitarras"
              :key="guitarra.id"
              :guitarra="guitarra"
              @agregar-carrito="agregarCarrito"
          />
      </div>
  </main>
  <Footer />
</template>
