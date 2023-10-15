<template>
  <div>
    <h1>Generador de Números para Rifa</h1>

    <div class="alineacionContenido">
      <label for="valorInicial">Valor inicial:</label>
      <input v-model="valorInicial" type="number">
    </div>

    <div class="alineacionContenido">
      <label for="valorFinal">Valor final:</label>
      <input v-model="valorFinal" type="number">
    </div>

    <div class="alineacionContenido">
      <label for="noHojas">Número Hojas:</label>
      <input v-model="noHojas" type="number">
    </div>

    <div class="alineacionContenido">
      <label for="agregarCaracteres">Agregar Caracteres:</label>
      <input class="tamCheck" v-model="agregarCaracteres" type="checkbox">
    </div>

    <div class="alineacionContenido" v-if="agregarCaracteres">
      <label for="caracteres">Ingresa caracteres:</label>
      <input v-model="caracteres" type="text">
    </div>
    <div class="espacioBTN">
      <button @click="generarNumeros">Generar Números</button>
      <button @click="crearPDF" v-if="numeros.length > 0">Crear PDF</button>
    </div>
    

    <div class="casoParticular">
      <span>Los valores iniclaes son un caso Particular.</span>
      <span> Se recomienda solo cambiar v. inical y v. final</span>
    </div>

    <table ref="tableToPDF" class="fixed-table">
      <tbody>
        <template v-for="i in Math.ceil(numeros.length / 8)">
          <tr>
            <td v-for="j in 8" :key="j" class="number-cell">
              {{ agregarCaracteres ? `${caracteres}${numeros[(i - 1) * 8 + j - 1]}` : numeros[(i - 1) * 8 + j - 1] }}
            </td>
          </tr>
        </template>
      </tbody>
    </table>
  </div>
</template>

<script>
import jsPDF from 'jspdf';
import 'jspdf-autotable';

export default {
  data() {
    return {
      valorInicial: 1,
      valorFinal: 52,
      noHojas: 1,
      agregarCaracteres: false,
      caracteres: '',
      numeros: [],
    };
  },
  methods: {
    generarNumeros() {
      this.numeros = [];
      for (let i = this.valorInicial; i <= this.valorFinal*this.noHojas; i++) {
        this.numeros.push(i);
        this.numeros.push(i); // Duplicar el número
        if (this.agregarCaracteres) {
          this.numeros.push(`${this.caracteres}_${i}`);
          this.numeros.push(`${this.caracteres}_${i}`); // Duplicar el número con caracteres
        }
      }
    },
    crearPDF() {
      const doc = new jsPDF('L', 'pt', 'letter');
      const table = this.$refs.tableToPDF;

      doc.autoTable({
        html: table,
        theme: 'grid',
        styles: {
          fontSize: 30, // Tamaño de texto (ajusta según tus necesidades)
          cellPadding: 5,
          minCellHeight: 2,
          lineColor: [0, 0, 0],
          halign: 'center'
        },
        tableLineColor: [0, 0, 0],
        margins: {
          top: 15,
          bottom: 15,
          left: 18,
          right:18
        }
      });

      doc.save('tabla_rifa.pdf');
    },
  },
};
</script>

<style>
/* Estilo para las casillas de números */
.number-cell {
  border: 25px 50px; /* Tamaño del borde: 25px arriba/abajo, 30px izquierda/derecha */
  padding: 5px 8px; /* Espacio adicional dentro de la casilla para el contenido */
  text-align: center;
  font-size: 10px;
}

table.fixed-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  
  text-align: center;
}

table.fixed-table th, table.fixed-table td {
  border: 1px solid #ccc;
  text-align: center;
  overflow: hidden; /* Evitar que el contenido expanda las casillas */
  white-space: nowrap; /* Evitar el salto de línea en el contenido largo */
  text-overflow: ellipsis; /* Mostrar "..." en contenido largo */
}

table.fixed-table th {
  background-color: #007BFF;
  color: #fff;
  font-weight: bold;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

tr:nth-child(odd) {
  background-color: #fff;
}
</style>
