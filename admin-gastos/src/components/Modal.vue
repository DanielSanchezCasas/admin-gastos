<script setup>
import { ref } from 'vue';
import Alerta from "./Alerta.vue";
import cerrarModal from "../assets/img/cerrar.svg";

const error = ref('');

const emit = defineEmits(['cerrar-modal', 'guardar-gasto', 'eliminar-gasto', 'update:nombre', 'update:cantidad', 'update:categoria']);
const props = defineProps({
    modal: {
        type: Object,
        required: true,
    },
    nombre: {
        type: String,
        required: true,
    },
    cantidad: {
        type: [Number, String],
        required: true,
    },
    categoria: {
        type: String,
        required: true,
    },
    disponible: {
        type: Number,
        required: true,
    },
    id: {
        type: [null, String],
        required: true,
    }
})

const oldCantidad = props.cantidad;

const agregarGasto = () => {
    const { nombre, cantidad, categoria, disponible, id } = props;
    if ([nombre, cantidad, categoria].includes('')) {
        error.value = 'Todos los campos son obligatorios';
        setTimeout(() => {
            error.value = '';
        }, 3000);
        return;
    }
    if (cantidad <= 0) {
        error.value = 'La cantidad no es valida';
        setTimeout(() => {
            error.value = '';
        }, 3000);
        return;
    }
    if (id) {
        if(cantidad > oldCantidad + disponible){
            error.value = 'Has exedido el tope de tu presupuesto';
            setTimeout(() => {
                error.value = '';
            }, 3000);
            return;
        }
    } else {
        if (cantidad > disponible) {
            error.value = 'Has exedido el tope de tu presupuesto';
            setTimeout(() => {
                error.value = '';
            }, 3000);
            return;
        }
    }

    emit('guardar-gasto');
}
</script>

<template>
    <div class="modal">
        <div class="cerrar-modal">
            <img :src="cerrarModal" alt="boton cerrar modal" @click="$event => $emit('cerrar-modal')">
        </div>
        <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
            <form class="nuevo-gasto" @submit.prevent="agregarGasto">
                <legend>{{  id ? 'Editar gasto': 'Añadir gasto' }}</legend>
                <Alerta v-if="error">{{ error }}</Alerta>
                <div class="campo">
                    <label for="nombre">Nombre gasto:</label>
                    <input type="text" id="nombre" placeholder="Agrega el nombre del gasto" :value="nombre"
                        @input="$event => $emit('update:nombre', $event.target.value)">
                </div>
                <div class="campo">
                    <label for="cantidad">Cantidad:</label>
                    <input type="text" id="number" placeholder="Agrega la cantidad del gasto" :value="cantidad"
                        @input="$event => $emit('update:cantidad', +$event.target.value)">
                </div>
                <div class="campo">
                    <label for="categoria">Categoria:</label>
                    <select id="categoria" :value="categoria"
                        @input="$event => $emit('update:categoria', $event.target.value)">
                        <option value="">--Seleccione--</option>
                        <option value="ahorro">Ahorro</option>
                        <option value="comida">Comida</option>
                        <option value="casa">Casa</option>
                        <option value="gastosvarios">Gastos Varios</option>
                        <option value="ocio">Ocio</option>
                        <option value="salud">Salud</option>
                        <option value="suscripciones">Suscripciones</option>
                    </select>
                </div>
                <input type="submit" :value="  [id ? 'Editar gasto': 'Añadir gasto'] ">
            </form>
            <button type="button" class="btn-eliminar" v-if="id" @click="$event => $emit('eliminar-gasto')">Eliminar Gasto</button>
        </div>
    </div>
</template>

<style scoped>
.modal {
    position: absolute;
    background-color: rgb(0 0 0 / 0.9);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
}

.cerrar-modal {
    position: absolute;
    right: 3rem;
    top: 3rem;
}

.cerrar-modal img {
    width: 3rem;
    cursor: pointer;
}

.contenedor-formulario {
    transition-property: all;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
    opacity: 0;
}

.contenedor-formulario.animar {
    opacity: 1;
}

.contenedor-formulario.cerrar {
    opacity: 0;
}

.nuevo-gasto {
    margin: 10rem auto 0 auto;
    display: grid;
    gap: 2rem;
}

.nuevo-gasto legend {
    text-align: center;
    color: var(--blanco);
    font-size: 3rem;
    font-weight: 700;
}

.campo {
    display: grid;
    gap: 2rem;
}

.nuevo-gasto input,
.nuevo-gasto select {
    background-color: var(--gris-claro);
    border-radius: 1rem;
    padding: 1rem;
    border: none;
    font-size: 2.2rem;
}

.nuevo-gasto label {
    color: var(--blanco);
    font-size: 3rem;
}

.nuevo-gasto input[type="submit"] {
    background-color: var(--azul);
    color: var(--blanco);
    font-weight: 700;
    cursor: pointer;
}
.btn-eliminar{
    padding: 1rem;
    width: 100%;
    background-color: #ef4444;
    font-weight: 700;
    color: var(--blanco);
    margin-top: 10rem;
    cursor: pointer;
    border: none;
}
</style>