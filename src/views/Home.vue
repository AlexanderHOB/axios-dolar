<template>
  <div>
    <v-container text-center>
      <v-layout wrap>
        <v-flex xs12> 
          <v-card>
            <v-date-picker 
              v-model="fecha" 
              header-color="blue"
              full-width
              locale="es-pe"
              :min="fechaMinimo"
              :max="fechaMaximo"
              @change="getDolar(fecha)">
            </v-date-picker>
          </v-card>
          <v-card color="error" dark>
            <v-card-text class="display-1 white--text">
                {{valor}} - {{fecha}}
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container> 
  </div>
</template>

<script>
import axios from "axios";
import {mapMutations} from "vuex";
export default {
  data(){
    return {
      fecha:new Date().toISOString().substr(0, 10),
      fechaMinimo:'1984',
      fechaMaximo:new Date().toISOString().substr(0, 10),
      valor:null,
    }
  },
  methods:{
    ...mapMutations(['mostrarLoading','ocultarLoading']),
    async getDolar(dia){
      let arrayFecha= dia.split('-');
      let ddmmyy=arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0];
      try{
        this.mostrarLoading({
          titulo:'Accediendo Información',
          color:'secondary'
        })
        let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)
        if(datos.data.serie.length>0){
          console.log(datos.data.serie[0].valor);
          this.valor=await datos.data.serie[0].valor;
        }else{
          this.valor='Sin resultado'
        }
      } catch(e){
        console.log(e);
      } finally{
        this.ocultarLoading()
      }
    }
  },
  created(){
    this.getDolar(this.fecha);
  }
};
</script>
