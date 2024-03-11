<script setup>
    import { ref, reactive, onMounted, watch} from 'vue';
    import {db} from './date/guitarras' 
    import {dbrespvip} from './date/guitarravip'
    // Importar mis componentes
    import Guitarra from './components/Guitarra.vue';
    import Header from './components/Header.vue';
    import Footer from './components/Footer.vue';

    const guitarras = ref([])
    const guitarraVip = ref({})
    let carrito = ref([]) 
    
    watch(carrito, () => {
        guardarLocalStorage();
    },{
        deep: true 
    })

    onMounted(() => {        
        guitarras.value = db;
        guitarraVip.value = dbrespvip[0]; 
        
        const carritoStorange = localStorage.getItem('carrito')
        if (localStorage) {
                 carrito.value = JSON.parse(carritoStorange)
        }
    });

    const guardarLocalStorage = () => {
        localStorage.setItem('carrito',JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) => {
        const existeGuitarra = carrito.value.findIndex(elemento => elemento.id === guitarra.id)
        if (existeGuitarra >= 0){
            carrito.value[existeGuitarra].cantidad++
        } else{
            carrito.value.push(guitarra);
            guitarra.cantidad = 1;
        }
    }
    
    

    const decrementarCantidad = (id) => {
        const contador = carrito.value.findIndex(elemento => elemento.id === id)
        if (carrito.value[contador].cantidad <= 1) return
        carrito.value[contador].cantidad--
    }
    
    const incrementarCantidad = (id) => {
        const contador = carrito.value.findIndex(elemento => elemento.id === id)
        carrito.value[contador].cantidad++       
    }
    
    const eliminarProducto = (id) => {
        carrito.value = carrito.value.filter(producto => producto.id !== id)
    }

    const vaciarCarrito = () => {
        carrito.value = []
    }
</script>

<template>
    <Header
        :guitarraVip="guitarraVip"
        :carrito="carrito"
        @decrementar="decrementarCantidad"
        @incrementar="incrementarCantidad"
        @eliminar-producto="eliminarProducto"
        @agregar-carrito="agregarCarrito"
        @vaciar-Carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Productos</h2>

        <div class="row mt-5"><!--Contenido de las guitarras-->
            
            <!-- Renderizando mi componente (Una de las formas)-->
            <Guitarra
                v-for="guitarra in guitarras"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito" 
            /> <!-- los : son equivalentes a decis la directris v-bind -->
        </div>
    </main>

    <Footer />
</template>

