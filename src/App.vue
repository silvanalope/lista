<template>
  <div class="container">
    <div id="apptareas">
      <div class="row mt-4"> <p> registro de nombres</p>
        <div   class="col ">
          <input type="text" class="form-control"  placeholder="ingresar nombre" v-model="txttarea   " >
        </div>
        
        <div class="col">
          <input type="text" class="form-control"  placeholder="ingresar apellido" v-model="txttarea1" > 
        </div>
        <div  class="col">
          <button  type="button" class="btn btn-outline-primary" @click="agregartarea" >  {{cambiar}} </button>
          <button type="button" class="btn btn-outline-success" v-show="shoa" @click="cancelar()" > cancelar</button>
        </div>
      </div>
       <div class= "estilo">
       <p>Se ha hecho clic en el botón de  {{cambiar}} {{ counter }} veces.</p>
       <p>Se ha hecho clic en el botón de eliminar {{ contar }} veces.</p>
       <p>Se ha hecho clic en el botón de editar {{ cont }} veces.</p>
       </div>

      <ul v-for="tarea of listatareas" :key="tarea.id " >
        <li>
          <label  class="pading">{{tarea.texto}}   </label> 
           <label class="pading">{{tarea.textos}}</label>
          <button  type="button" class="btn btn-outline-danger"  @click="eliminartarea(tarea)">eliminar </button>
          <button  type="button" class="btn btn-outline-primary" @click=" shoa=true, modificarlista(tarea) " >editar</button>
          
          
        </li>
      </ul>
     
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: "apptareas",
  data() {
   
    return {
      usuario: "silvia",
      txttarea: "",
      txttarea1: "",
      listatareas: [
        {
          texto: ' nombre',
          textos:' apellido'

        }
      ],

      cambiar: "agregar",
      counter:0,
      contar:0,
      cont:0,
      shoa:false,

     
      

    };
  },
   async mounted (){
     let data = await axios.get('http://perrito.test/api/lista')
      console.log('ddddd', data.data); 
      this.listatareas = data.data.map(item => {
        return {
          texto: item.nombre, 
          textos: item.apellido,
          id:item.id
        }
      } );
  },
  methods: {

  async  agregartarea() {
      
      this.shoa= false
 
      let estaEnEdicion = false
      let indiceTareaEnEdicion = 0
      this.listatareas.forEach((kkk, ijise) => {
        if (kkk.estaEnEdicion == true) {
          estaEnEdicion = true
          indiceTareaEnEdicion = ijise
        }
      })
      if (estaEnEdicion) {
      
        this.listatareas[indiceTareaEnEdicion].texto = this.txttarea
         this.listatareas[indiceTareaEnEdicion].textos = this.txttarea1
         
         this.listatareas[indiceTareaEnEdicion].estaEnEdicion = false
        this.cont+=1
      
      } 
      else {
        var  tarea = this.txttarea.trim();
        var tarea2 = this.txttarea1.trim();
        if (tarea && tarea2) {
          this.listatareas.push({
            texto: tarea,
            textos:tarea2,
            id: Math.floor(Math.random() * 20)
            
          });
            await axios.post(`http://perrito.test/api/lista`,{
              nomb: tarea,
              apell: tarea2
           })
           location.reload();
        }
        this.counter+=1
        
      }
         
      this.cambiar= "agregar"
          
      this.txttarea = "";
      this.txttarea1 = "";
    },
    

     

   async eliminartarea(tarea){
      
      var ind = this.listatareas.indexOf(tarea);
      this.listatareas.splice(ind,1)
      this.contar+=1
      console.log(tarea.id);
     await axios.delete(`http://perrito.test/api/lista/${tarea.id}`)
      },

      modificarlista(tarea) {
        
       this.listatareas.forEach(element => {
        if (element == tarea) {
          element.estaEnEdicion = true
          this.txttarea = element.texto
          this.txttarea1 = element.textos
        } else {
          element.estaEnEdicion = false
        }  
      })
        this.cambiar= "agregar edicion"
      console.log(this.listatareas)
      },

      cancelar(){
        this.shoa=false
       
       this.txttarea=""
       this.txttarea1 =""
       this.cambiar= "agrgar"
        
        

        
      }

    }
 }

</script>
<style>

.titulo {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  border-style: solid;
  border-color: #1b4332;
  background-color: #40916c;
  max-width: 900px;
  margin-left: 250px;
}
.lista {
  text-align: center;
}
#tareas {
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

  .fade-enter, .fade-leave-to {
    opacity: 0.8
  }
  .estilo{
     font-family: monospace, Helvetica, Arial, sans-serif;
     color: black;
  }
   .container{
     background: #ffe8d6;
     padding: 10px;
     margin-block: 23px;
     border-radius: 20px;
   }

  #apptareas{
    font-family: cursive;
    color: green;
  }


  .pading{
    padding-left:10px ;
  }
  
   
  
</style>