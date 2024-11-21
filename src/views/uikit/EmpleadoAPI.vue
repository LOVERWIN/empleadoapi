<template>
    <div class="datatable-responsive">
        <h2>Lista de Empleados</h2>

        <!-- Formulario con campos de ID, Nombre, Usuario, y Correo -->
        <div class="user-form">
            <h3>Consultar Usuario</h3>
            <form @submit.prevent="handleSubmit">
                <label for="id">ID:</label>
                <input 
                    v-model="selectedUser.id" 
                    type="text" 
                    id="id" 
                    placeholder="Ingresa el ID del usuario"
                />

                <label for="name">Nombre:</label>
                <input 
                    v-model="selectedUser.name" 
                    type="text" 
                    id="name" 
                    placeholder="Nombre del usuario" 
                    disabled
                />

                <label for="username">Usuario:</label>
                <input 
                    v-model="selectedUser.username" 
                    type="text" 
                    id="username" 
                    placeholder="Usuario" 
                    disabled
                />

                <label for="email">Correo:</label>
                <input 
                    v-model="selectedUser.email" 
                    type="email" 
                    id="email" 
                    placeholder="Correo electrónico" 
                    disabled
                />

                <button type="submit" class="consult-button">Consultar</button>
            </form>
        </div>

        <!-- Tabla de resultados -->
        <DataTable 
            :value="filteredEmployees" 
            paginator 
            :rows="5" 
            :rowsPerPageOptions="[5, 10, 15]" 
            responsiveLayout="scroll" 
            :loading="loading" 
            class="p-datatable-sm"
            @row-click="handleRowClick"
        >
            <Column field="id" header="ID" sortable></Column>
            <Column field="name" header="Nombre" sortable></Column>
            <Column field="username" header="Usuario" sortable></Column>
            <Column field="email" header="Correo" sortable></Column>
        </DataTable>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';

export default {
    components: {
        DataTable,
        Column
    },
    setup() {
        const employees = ref([]); // Todos los empleados
        const filteredEmployees = ref([]); // Resultados filtrados
        const loading = ref(false); // Indicador de carga
        const selectedUser = ref({
            id: '',
            name: '',
            username: '',
            email: ''
        }); // Datos del usuario seleccionado

        // Obtener todos los empleados
        const fetchEmployees = async () => {
            loading.value = true;
            try {
                const response = await axios.get('https://jsonplaceholder.typicode.com/users');
                employees.value = response.data; // Todos los usuarios
                filteredEmployees.value = response.data; // Mostrar todos los usuarios por defecto
                loading.value = false;
            } catch (error) {
                console.error('Error al obtener los empleados:', error);
                loading.value = false;
            }
        };

        // Consultar por ID
        const handleSubmit = async () => {
            if (selectedUser.value.id.trim() === '') {
                // Si el ID está vacío, mostrar todos los empleados
                filteredEmployees.value = employees.value;
            } else {
                try {
                    const response = await axios.get(`https://jsonplaceholder.typicode.com/users/${selectedUser.value.id}`);
                    filteredEmployees.value = [response.data]; // Mostrar el usuario consultado
                } catch (error) {
                    console.error('Error al obtener el usuario:', error);
                }
            }
        };

        // Manejar clic en una fila de la tabla para cargar los datos en el formulario
        const handleRowClick = (event) => {
            selectedUser.value = { ...event.data }; // Llenar el formulario con los datos de la fila seleccionada
        };

        // Llamar a fetchEmployees al montar el componente para obtener todos los usuarios
        onMounted(() => {
            fetchEmployees();
        });

        return {
            employees,
            filteredEmployees,
            loading,
            selectedUser,
            handleSubmit,
            handleRowClick
        };
    }
};
</script>

<style>
.datatable-responsive {
    padding: 2rem;
}

.user-form {
    margin-bottom: 1rem;
}

.user-form form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.user-form input {
    padding: 0.5rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
}

.user-form button {
    padding: 0.5rem 1rem;
    font-size: 1rem;
    color: #fff;
    background-color: #007bff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.user-form button:hover {
    background-color: #0056b3;
}

.consult-button {
    background-color: #007bff;
}

.consult-button:hover {
    background-color: #0056b3;
}
</style>
