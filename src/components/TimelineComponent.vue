<template>
  <div>
    <!-- Caja de formulario con bordes y sombra, incluyendo el título "Reservar" -->
    <div class="form-container">
      <!-- Título de la barra de tareas dentro del rectángulo -->
      <div class="toolbar">
        <h1>Reservar</h1>
      </div>

      <!-- Formulario de reserva con los campos requeridos -->
      <div class="reservation-form">
        <div class="form-group">
          <label for="name">Nombre:</label>
          <input v-model="newReservation.name" id="name" placeholder="Ej: Juan Pérez" />
        </div>

        <div class="form-group">
          <label for="date">Fecha:</label>
          <div class="input-with-icon">
            <input v-model="newReservation.date" type="date" id="date" placeholder="30/9/2025" />

          </div>
        </div>

        <div class="form-group">
          <label for="startTime">Desde:</label>
          <div class="input-with-icon">
            <input v-model="newReservation.startTime" type="time" id="startTime" placeholder="12:00" />

          </div>
        </div>

        <div class="form-group">
          <label for="endTime">Hasta:</label>
          <div class="input-with-icon">
            <input v-model="newReservation.endTime" type="time" id="endTime" placeholder="13:00" />

          </div>
        </div>

        <div class="form-group">
          <button @click="addReservation">Guardar</button>
        </div>
      </div>
    </div>

    <!-- Contenedor del timeline (más angosto) -->
    <div ref="visualization" class="timeline-container"></div>
  </div>
</template>

<script>
import { DataSet, Timeline } from 'vis-timeline/standalone';
import moment from 'moment';

export default {
  name: 'TimelineComponent',
  data() {
    return {
      newReservation: {
        name: '',
        date: '',
        startTime: '',
        endTime: ''
      },
      items: new DataSet([]) // Array donde almacenamos las reservas
    };
  },
  mounted() {
    const container = this.$refs.visualization;
    const options = {
      width: '100%',
      height: '150px', // Altura ajustada para que sea más angosto
    };

    new Timeline(container, this.items, options);
  },
  methods: {
    addReservation() {
      // Validación para asegurarse de que todos los campos estén llenos
      if (this.newReservation.name && this.newReservation.date && this.newReservation.startTime && this.newReservation.endTime) {
        // Crear las fechas completas a partir de los campos
        const startDateTime = moment(`${this.newReservation.date} ${this.newReservation.startTime}`, 'YYYY-MM-DD HH:mm').toDate();
        const endDateTime = moment(`${this.newReservation.date} ${this.newReservation.endTime}`, 'YYYY-MM-DD HH:mm').toDate();

        // Añadir la nueva reserva al timeline
        this.items.add({
          id: this.items.length + 1,
          content: this.newReservation.name,
          start: startDateTime,
          end: endDateTime
        });

        // Limpiar los campos del formulario después de guardar
        this.newReservation.name = '';
        this.newReservation.date = '';
        this.newReservation.startTime = '';
        this.newReservation.endTime = '';
      } else {
        alert('Por favor, completa todos los campos.');
      }
    }
  }
};
</script>

<style scoped>
/* Caja que envuelve el formulario y el título */
.form-container {
  max-width: 1000px;
  margin: 20px auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

/* Barra de tareas horizontal con título "Reservar" dentro del rectángulo */
.toolbar {
  background-color: #4CAF50;  /* Fondo verde */
  padding: 15px;
  color: white;
  text-align: center;
  margin-bottom: 20px; /* Margen inferior para separar la barra de tareas del formulario */
}

.toolbar h1 {
  margin: 0;
  font-size: 24px;
  font-weight: bold;
}

/* Estilo para el formulario de reservas */
.reservation-form {
  display: flex;
  justify-content: space-between;
  gap: 20px; /* Espaciado entre los campos */
  flex-wrap: wrap;
}

/* Estilo para cada campo del formulario */
.form-group {
  display: flex;
  flex-direction: column;
  flex: 1 1 22%;  /* Establecer el ancho de los campos */
  margin-bottom: 20px; /* Margen inferior para separar cada campo */
}

.form-group label {
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 15px;  /* Separar la etiqueta del campo */
  padding-left: 12px;
}

.form-group input {
  padding: 12px;
  font-size: 14px;
  border-radius: 5px;
  border: 1px solid #ddd;
  width: 100%;
  margin-bottom: 10px;
  transition: border-color 0.3s ease;
}

.form-group input:focus {
  border-color: #4CAF50;
  outline: none;
}

.form-group button {
  padding: 12px 20px;
  background-color: #4CAF50;
  color: white;
  font-size: 14px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.form-group button:hover {
  background-color: #45a049;
}

/* Estilo para los campos con icono */
.input-with-icon {
  position: relative;
  display: flex;
  align-items: center;
}

.input-with-icon input {
  padding-right: 35px;  /* Espacio para el icono */
}

.input-with-icon i {
  position: absolute;
  right: 10px;
  font-size: 20px;
  color: #4CAF50;
}

/* Estilo para el timeline (más angosto) */
.timeline-container {
  width: 100%;
  height: 150px; /* Altura más angosta */
  border: 1px solid #ddd;
  margin-top: 20px;
}
</style>
