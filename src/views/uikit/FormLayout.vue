<template>
    <Fluid>
        <div class="flex flex-col md:flex-row gap-8">
            <div class="md:w-1/2">
                <div class="card flex flex-col gap-4">
                    <div class="font-semibold text-xl">Vertical</div>
                    <div class="flex flex-col gap-2">
                        <label for="nombre">Nombre</label>
                        <InputText v-model="nombre" id="nombre" type="text" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="apellidop">Apellido Paterno</label>
                        <InputText v-model="apellidoP" id="apellidop" type="text" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="apellidom">Apellido Materno</label>
                        <InputText v-model="apellidoM" id="apellidom" type="text" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="birthDate">Fecha de nacimiento:</label>
                        <InputText v-model="fechaNacimiento" id="birthDate" type="date" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="RFC">RFC</label>
                        <InputText v-model="rfc" id="RFC" type="text" readonly />
                    </div>
                    <button 
                        type="button" 
                        @click="generarRFC"
                        style="background-color: green; color: white; border: none; padding: 10px 20px; cursor: pointer;">
                        Generar
                    </button>
                </div>
            </div>
        </div>
    </Fluid>
</template>

<script setup>
import { ref } from 'vue';

const nombre = ref('');
const apellidoP = ref('');
const apellidoM = ref('');
const fechaNacimiento = ref('');
const rfc = ref('');

const generarRFC = () => {
    const letra1 = apellidoP.value.charAt(0).toUpperCase(); // Primera letra del apellido paterno
    const letra2 = apellidoP.value.charAt(1).toUpperCase(); // Segunda letra del apellido paterno
    const letra3 = apellidoM.value.charAt(0).toUpperCase(); // Primera letra del apellido materno
    const letra4 = nombre.value.charAt(0).toUpperCase(); // Primera letra del nombre
    
    // Formato de la fecha (YYYY-MM-DD)
    const fecha = fechaNacimiento.value;
    const anio = fecha.split('-')[0].slice(-2); // Obtiene los últimos 2 dígitos del año
    const mes = fecha.split('-')[1]; // Obtiene el mes
    const dia = fecha.split('-')[2]; // Obtiene el día
    
    // Construye el RFC
    rfc.value = '${letra1}${letra2}${letra3}${letra4}${anio}${mes}${dia}';
};
</script>