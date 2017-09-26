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
                <textarea placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                <a class="button is-primary">Enviar</a>
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
        search: '',
        compromissos: [],
        compromissosDet: [],
        selected: {},
        itensPerPage: 10,
        showModalNew: false,
        showModalForum: false,
        tipos: [],
        status: [],
        visivel: false
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

        let qString = 479;

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(ENDPOINT + `api/comp/obterCompCab?idComp=${qString}`).then(
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

        let qString = 479;

        if (this.search){
          qString = `&q=${this.search}`
        }
        
        this.$http.get(ENDPOINT + `api/comp/obterCompdet?idComp=${qString}`).then(
         response=>{
           t.compromissosDet = response.json()
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

       
       },
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
