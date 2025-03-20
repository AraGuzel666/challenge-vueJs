<template>
  <div>
    <!-- Caja de formulario con bordes y sombra, incluyendo el título "Reservar" -->
    <div class="form-container">
      <div class="toolbar">
        <h1>Reservar</h1>
      </div>
      <!-- Formulario de reserva con los campos requeridos -->
      <div class="reservation-form">
        <div class="form-group">
          <label for="name">Nombre</label>
          <input v-model="newReservation.name" id="name" placeholder="Ej: Juan Pérez" />
        </div>

        <div class="form-group">
          <label for="date">Fecha</label>
          <input v-model="newReservation.date" type="date" id="date" />
        </div>

        <div class="form-group">
          <label for="startTime">Desde</label>
          <input v-model="newReservation.startTime" type="time" id="startTime" />
        </div>

        <div class="form-group">
          <label for="endTime">Hasta</label>
          <input v-model="newReservation.endTime" type="time" id="endTime" />
        </div>

        <div class="form-group">
          <button @click="addReservation">Guardar</button>
        </div>
      </div>
    </div>





    <!-- Contenedor del timeline -->
    <div ref="visualization" class="timeline-container"></div>


    <!-- Componente de la tabla dinámica -->
    <CrudComponent :reservations="reservations" @deleteReservation="deleteReservation" />
  </div>
</template>

<script>
import { DataSet, Timeline } from 'vis-timeline/standalone';
import moment from 'moment';
import CrudComponent from './CrudComponent.vue';

export default {
  name: 'TimelineComponent',
  components: {
    CrudComponent
  },
  data() {
    return {
      newReservation: {
        name: '',
        date: '',
        startTime: '',
        endTime: ''
      },
      items: new DataSet([]),
      reservations: []
    };
  },
  mounted() {
    // Cargar reservas desde localStorage
    const storedReservations = localStorage.getItem('reservations');
    if (storedReservations) {
      this.reservations = JSON.parse(storedReservations);

      this.reservations.forEach(reservation => {
        this.items.add({
          id: reservation.id,
          content: reservation.name,
          start: new Date(reservation.start),
          end: new Date(reservation.end)
        });
      });
    }

    // Inicializar Timeline
    const container = this.$refs.visualization;
    const options = {
      width: '100%',
      height: '150px'
    };
    new Timeline(container, this.items, options);
  },
  methods: {
    
    addReservation() {
      if (this.newReservation.name && this.newReservation.date && this.newReservation.startTime && this.newReservation.endTime) {
    const startDateTime = moment(`${this.newReservation.date} ${this.newReservation.startTime}`, 'YYYY-MM-DD HH:mm').toDate();
    const endDateTime = moment(`${this.newReservation.date} ${this.newReservation.endTime}`, 'YYYY-MM-DD HH:mm').toDate();

    const reservation = {
      id: Date.now(),
      name: this.newReservation.name,
      start: startDateTime,
      end: endDateTime,
      status: 'myReservation'  // Añadir el estado aquí
    };

    this.reservations.push(reservation);
    this.items.add({
      id: reservation.id,
      content: reservation.name,
      start: startDateTime,
      end: endDateTime,
      className: this.getStatusClass(reservation.status),  // Usar una función para obtener la clase según el estado
      title: this.getStatusTitle(reservation.status),  // Título con el estado (opcional)
    });

    // Guardar en localStorage
    localStorage.setItem('reservations', JSON.stringify(this.reservations));

    // Limpiar formulario
    this.newReservation.name = '';
    this.newReservation.date = '';
    this.newReservation.startTime = '';
    this.newReservation.endTime = '';
  } else {
    alert('Por favor, completa todos los campos.');
  }
    },
    deleteReservation(id) {
      this.reservations = this.reservations.filter(reservation => reservation.id !== id);
      this.items.remove(id);

      // Actualizar localStorage después de eliminar
      localStorage.setItem('reservations', JSON.stringify(this.reservations));
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
  display: flex;
  flex-direction: column;
}

/* Barra de tareas horizontal con título "Reservar" dentro del rectángulo */
.toolbar {
  padding: 15px;
  color: white;
  text-align: center;
  margin-bottom: 25px;
  width: 100%;
}

.toolbar h1 {
  color: #007F6E;
  margin: 0;
  font-size: 24px;
  font-weight: bold;
  text-transform: uppercase;
  text-align: left;

}

/* Estilo para el formulario de reservas */
.reservation-form {
  display: flex;
  flex-flow: row;
  justify-content: center;
  align-items: center;
  gap: 25px;/* Espaciado entre los campos */
  width: 100%;
}

/* Estilo para cada campo del formulario */
.form-group {
  display: flex;
  flex-direction: column;
  flex: 1 1 22%;  /* Establecer el ancho de los campos */
  margin-bottom: 25px;

}


.form-group label {
  font-size: 14px;
  margin-bottom: 25px;/* Separar la etiqueta del campo */
  padding-left: 12px;
}

.form-group input {
  padding: 12px;
  font-size: 14px;
  border-radius: 5px;
  border: 1px solid #ddd;
  margin-bottom: 25px;
  transition: border-color 0.3s ease;
  

}

.form-group input:focus {
  border-color: #4CAF50;
  outline: none;
}

.form-group button {
  padding: 12px;
  background-color: #007F6E;
  color: white;
  font-size: 14px;
  font-weight: bold;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  height: 46px;
  width: 75%;
}

.form-group button:hover {
  background-color: #1cc6af;
}

/* Estilo para los campos con icono */
.input-with-icon {

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
