<template>
  <i class="fixo fa fa-spinner fa-pulse fa-5x fa-fw" v-show="isLoading"></i>
  <span class="fixo sr-only" v-show="isLoading">Carregando...</span>
  <div class="container">
      <!-- cabeçario -->
    <div class="card" style="width: 100%;">
      <header class="card-header">
        <p class="card-header-title">
          Compromisso: {{compromissos.idComp}}
        </p>
      </header>
      <div class="card-content">
          
      
        <section>
            
          <div class="columns">
              
            <div class="column">
              <label class="label">Data:</label>
                {{compromissos.dataHora}}
            </div>
              
            <div class="column">
              <label class="label">Tipo:</label>
                {{compromissos.tipoComp}}
            </div>
              
            <div class="column is-4">
              <label class="label">Status:</label>
                {{compromissos.status}}
            </div>
              
          </div>
          
          <div class="columns">
            <div class="column">
              <label class="label">Título:</label>
                {{compromissos.titulo}}
            </div>
            <div class="column is-4">
              <label class="label">Projeto:</label>
                {{compromissos.projeto}}
            </div>
          </div>
            
        
          <div class="columns">
            <div class="column">
              <label class="label">Plataforma:</label>
                {{compromissos.plataforma}}
            </div>
            <div class="column">
              <label class="label">Prioridade:</label>
                {{compromissos.numPrioridade}}
            </div>
            <div class="column">
              <label class="label">Usuário:</label>
                {{compromissos.usuario}}
            </div> 
          </div>
          
          <br>
          
        </section>
      </div>
      
    </div>
      
    <br>
      
    <h2>Tópico: {{ compromissos.titulo }}</h2>
    <br>
      
      <!-- detalhes -->
    <div v-for="compromisso in compromissosDet">
    
        <div class="box">
            <strong>cód:</strong>
            <strong>{{compromisso.idCompDet}}</strong> 
            <hr style="margin-top: 0;">
            <div class="columns">
                <div class="column">
                <label class="label">Mensagem:</label>
                {{compromisso.detalhes}}
                </div>
            </div>
            <div class="columns">
                <div class="column">
                    <strong>Data/Hora de Abertura:</strong>
                    <div>{{compromisso.dataHoraAgend}}</div>
               
                </div>    
                <div class="column">
                    <strong>Data/Hora de Atendimento:</strong>
                    <div>{{compromisso.dataHoraAtend}}</div>
               
                </div>
            </div>    
            
            
            <a class="button is-primary" @click="showResposta" >Responder</a>
            <br><br>
            <div  v-if="visivel">
                <textarea v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                
                <div class="columns">
                    <div class="column">
                        <a class="button is-primary" @click.prevent="salvarDet()">Enviar</a>
                    </div>    
                    <div class="column">
                        <strong>Data:</strong>{{ currentTime }}
                    </div>
                    
                    <div class="column">
                        <strong>Hora:</strong>{{ currentHour }}
                    </div>
                    
                </div>   
            </div>
        </div>
        
        
             
   </div>                
 </div>   
</template>

<script>

import axios from 'axios'
//produção
const ENDPOINT = 'http://192.168.0.200/helpdesk/'
//debug
// const ENDPOINT = 'http://192.168.0.115:32688/'

  var moment = require('moment');
  require("moment/min/locales.min");
  moment.locale('pt-br');


export default {
    name: 'CompromissosDet',
    data () {
      return {
        isLoading: false,
        title: 'Tópicos',
        currentTime: moment().format('L'),
        currentHour: moment().format('LT'),
        compromissos: [],
        compromissosDet: [],
        showModalNew: false,
        showModalForum: false,
        visivel: false,
        msg: '',
        q: 482,
        
        compDet: {
            "detalhes": "jsdkfhsk",
            "idComp": 482,
            "idUsuario": 4,
            "idStatus": 1,
            
        },
        
      }
    },
    props: [ "filtro" ],
    
    methods: {
      showResposta(){
        if(this.visivel==true){
            this.visivel=false
        }
        else {
            this.visivel=true;
        }
        
      },
      showLoading(){
        this.isLoading=true;
      },
      hideLoading(){
        this.isLoading=false;
      },
      loadCompromissos(){

        let t = this
        this.showLoading()

        let q = 482;

        this.$http.get(ENDPOINT + `api/comp/obterCompCab?idComp=${q}`).then(
         response=>{
           t.compromissos = response.json()
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

       },
      loadDetahes(){

        let t = this
        this.showLoading()

        let q = 482;
        
        this.$http.get(ENDPOINT + `api/comp/obterCompdet?idComp=${q}`).then(
         response=>{
           t.compromissosDet = response.json()
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })
      },
      salvarDet(){
          
             this.$http.post(ENDPOINT + 'api/comp/novoDet', this.compDet)
             .then((response) => {
                this.$set('compDet',{})
                this.showResposta()
                console.log(response.body)
             })
             .catch((error) => {
                swal({   title: `Falha ao enviar sua solicitação`,
                        html: `<strong>É importante verificar se todos os campos estão preenchidos, caso contrário contate o admin</strong>`,   
                        type: "error",  
                    })
                //=>CAPTURAR O RETORNO DO SERVIDOR NA MENSAGEM
                /*this.err = JSON.stringify(error)
                swal({
                  html: '<strong>' + this.err + '</strong>',
                  confirmButtonText:
                    '<i class="fa fa-thumbs-up"></i> Ok!',
                }) */ 
                console.log(error);
             })
             .finally(function () {
                this.loadDetahes()
             })  
      
      }
       
    },
    created(){
      let t = this
      t.loadCompromissos()
      t.loadDetahes()
    }
}
</script>

<style scoped>
    h2 {
        font-size: 35px;
    }
</style>
