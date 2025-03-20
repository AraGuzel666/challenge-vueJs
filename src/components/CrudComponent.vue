<template>
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>Nombre</th>
            <th @click="sortByTime" style="cursor: pointer;">
              Horario (Desde - Hasta) 
              <span v-if="sortOrder === 'asc'">↑</span>
              <span v-if="sortOrder === 'desc'">↓</span>
            </th>
            <th>Acción</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="reservation in sortedReservations" :key="reservation.id">
            <td class="nombre">{{ reservation.name }}</td>
            <td>de {{ formatTime(reservation.start) }} a {{ formatTime(reservation.end) }}</td>
            <td>
              <button class="delete-button" @click="$emit('deleteReservation', reservation.id)">
                <i class="fas fa-trash-alt"></i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      reservations: Array
    },
    data() {
      return {
        sortOrder: 'asc', // Establecer el orden inicial como ascendente
      };
    },
    computed: {
      sortedReservations() {
        return [...this.reservations].sort((a, b) => {
          const startA = new Date(a.start).getTime();
          const startB = new Date(b.start).getTime();
  
          if (this.sortOrder === 'asc') {
            return startA - startB; // Orden ascendente
          } else {
            return startB - startA; // Orden descendente
          }
        });
      }
    },
    methods: {
      formatTime(date) {
        return new Date(date).toLocaleTimeString('es-ES', { hour: '2-digit', minute: '2-digit' });
      },
      sortByTime() {
        this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc'; // Alternar entre ascendente y descendente
      }
    }
  };
  </script>
  
  <style scoped>
  .table-container {
    margin-top: 20px;
    padding: 20px;
    background-color: #ffffff;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
  }
  
  th, td {
    padding: 15px 20px;
    border-bottom: 1px solid #e0e0e0;
    text-align: left;
    font-size: 1rem;
  }
  
  th {
    color: #333;
    font-weight: 600;
    background-color: #f9f9f9;
    text-transform: capitalize;
  }
  
  td {
    color: #555;
  }
  
  tr:hover {
    background-color: #f7f7f7;
  }
  
  .delete-button {
    background: none;
    border: none;
    cursor: pointer;
    padding: 8px 12px;
    color: #ccc;
    font-size: 1.4rem;
    transition: background-color 0.3s, color 0.3s;
    border-radius: 4px;
    text-align: center;
  }
  
  .delete-button:hover {
    color: #c9302c;
    background-color: #f8d7da;
  }
  
  .delete-button i {
    font-size: 1.4rem;
  }
  
  .nombre {
    color: #000;
    width: 75%;
    font-weight: bolder;
  }
  </style>
  