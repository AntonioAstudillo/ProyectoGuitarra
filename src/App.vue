<script setup>
    import { ref, onMounted , watch} from 'vue'
    import { db } from './data/guitarra'
    import Guitarra from './components/Guitarra.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'

    const guitarras = ref([]);
    const carrito = ref([]);
    const guitarra = ref({});

    watch(carrito, () => {
       guardarLocalStorage();
    }, {
        deep:true
    })

    onMounted(() => {
        guitarras.value = db;
        guitarra.value = db[3]
        const carritoStorage = localStorage.getItem('carrito');

        if(carritoStorage)
        {
            carrito.value = JSON.parse(carritoStorage);
        }
    });

    const agregarCarrito = (guitarra) => {

        const exists = carrito.value.findIndex(producto => producto.id === guitarra.id);


        if(exists >= 0) {
            carrito.value[exists].cantidad++;
        }else{

            guitarra.cantidad = 1; 
            carrito.value.push(guitarra);
        }  

        guardarLocalStorage();

        
    };

    const decrementarCantidad = (guitarra) =>{

        if(guitarra.cantidad > 1) {
            guitarra.cantidad--;
            
        } 
    };

    const incrementarCantidad = (guitarra) =>{

        if(guitarra.cantidad <6){
            guitarra.cantidad++;
            
        }
    };


    const eliminarElemento = (guitarra) => {

        let index = carrito.value.findIndex(elemento => elemento.id === guitarra.id);
        carrito.value.splice(index, 1);
        
    }


    const vaciarCarrito = () => {
        
        if(carrito.value.length > 0)
        {
            while (carrito.value.length > 0) {
                carrito.value.pop();
            }

            
        }
    }

    const guardarLocalStorage = () => {
        localStorage.setItem('carrito' , JSON.stringify(carrito.value));
    }

</script>


<template>
    <body>
        <Header 
            :carrito='carrito'   
            :guitarra='guitarra'
            @incrementar-cantidad="incrementarCantidad"    
            @decrementar-cantidad="decrementarCantidad"
            @eliminar-elemento="eliminarElemento"
            @agregar-carrito="agregarCarrito"
            @vaciar-carrito = "vaciarCarrito"
        />

        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
                <!-- AQUI DEBEMOS REDENRIZAR EL COMPONENTE DE GUITARRAS -->
                <Guitarra  v-for="guitarra in guitarras"  v-bind:guitarra = "guitarra"  @agregar-carrito="agregarCarrito"/>
            </div>
        </main>
        <Footer />
    </body>
</template>

