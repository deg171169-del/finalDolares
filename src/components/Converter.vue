<template>
  <div>
    <h1>Convertidor de Pesos a Dólares</h1>
    
    <input type="number" v-model="amountInPesos" placeholder="Ingrese monto en pesos" />
    <input type="number" v-model="dollarExchangeRate" placeholder="Cotización del dólar" :disabled="autoUpdate" />
    
    <div>
      <p>Resultado: {{ convertedAmount }} dólares</p>
      <p v-if="exchangeRateDate">Última actualización: {{ exchangeRateDate }}</p>
    </div>

    <label>
      <input type="checkbox" v-model="autoUpdate" />
      Habilitar actualización automática de cotización
    </label>
  </div>
</template>

<script>
import { ref, watch } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const amountInPesos = ref(0);
    const dollarExchangeRate = ref(0);
    const convertedAmount = ref(0);
    const autoUpdate = ref(false);
    const exchangeRateDate = ref('');

    // Función para actualizar la cotización
    const fetchExchangeRate = async () => {
      try {
        const response = await axios.get('https://api.bluelytics.com.ar/v2/latest');
        dollarExchangeRate.value = response.data.blue.value_buy;
        exchangeRateDate.value = new Date().toLocaleString();
      } catch (error) {
        console.error('Error al obtener la cotización:', error);
      }
    };

    // Observadores para actualizaciones automáticas
    watch([amountInPesos, dollarExchangeRate], () => {
      convertedAmount.value = (amountInPesos.value / dollarExchangeRate.value).toFixed(2);
    });

    watch(autoUpdate, (newVal) => {
      if (newVal) {
        fetchExchangeRate(); // Primera llamada inmediatamente
        const interval = setInterval(() => {
          fetchExchangeRate();
        }, 2000); // Actualiza cada 2 segundos
        
        // Limpiar el intervalo cuando se deshabilita
        return () => clearInterval(interval);
      }
    });

    return {
      amountInPesos,
      dollarExchangeRate,
      convertedAmount,
      autoUpdate,
      exchangeRateDate,
    };
  },
};
</script>

<style scoped>
/* Estilos opcionales */
</style>
