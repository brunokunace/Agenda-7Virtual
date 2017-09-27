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
      
     <!-- responder --> 
      
    <div class="box">
        <a class="button is-primary" @click="showResposta" >Responder</a>
            <br><br>
            <div  v-if="visivel">
                <textarea v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                
                <div class="columns">
                    <div class="column">
                        <a class="button is-primary" @click.prevent="salvarDet()">Enviar</a>
                    </div>    
                    <div class="column">
                        <strong>Data:</strong>
                        <div>
                          <date-picker :date="startTime" :option="option" :limit="limit"></date-picker>
                        </div>
                    </div>
                    <!--
                    <div class="column">
                        <strong>Hora:</strong>{{ currentHour }}
                    </div>
                    -->
                </div>   
            </div>  
    </div>
      
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
            
            
            
        </div>
        
        
             
   </div>                
 </div>   
</template>

<script>

import axios from 'axios'
import myDatepicker from 'vue-datepicker'
    
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
        compDet: {
            "detalhes": "teste 4",
            "idComp": 487,
            "idUsuario": 4,
            "idStatus": 1,
            "dataHoraAgend": "2017-09-27T17:20:12.513"
            
        },
          // datapicker
        startTime: {
            time: ''
        },
        endtime: {
            time: ''
        },

        option: {
            type: 'day',
            week: ['Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sab', 'Dom'],
            month: ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'],
            format: 'YYYY-MM-DD',
            placeholder: 'Insira a data',
            inputStyle: {
              'display': 'inline-block',
              'padding': '6px',
              'line-height': '22px',
              'font-size': '16px',
              'border': '2px solid #fff',
              'box-shadow': '0 1px 3px 0 rgba(0, 0, 0, 0.2)',
              'border-radius': '2px',
              'color': '#5F5F5F'
        },
        color: {
          header: '#1FC8DB',
          headerText: '#fff'
        },
        buttons: {
          ok: 'Ok',
          cancel: 'Cancel'
        },
        overlayOpacity: 0.5, // 0.5 as default
        dismissible: true // as true as default
      },
      timeoption: {
        type: 'min',
        week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
        month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
        format: 'YYYY-MM-DD HH:mm'
      },
      multiOption: {
        type: 'multi-day',
        week: ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'],
        month: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
        format:"YYYY-MM-DD HH:mm"
      },
      limit: [{
        type: 'weekday',
        available: [1, 2, 3, 4, 5]
        },
        {
        type: 'fromto',
        from: '2016-02-01',
        to: '2016-02-20'
        }
      ]
    
        }
    },
    components: {
    'date-picker': myDatepicker
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

        let q = 487;

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

        let q = 487;
        
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
                this.$set('compDet',{
                    "detalhes": '',
                    "idComp": 487,
                    "idUsuario": 4,
                    "idStatus": 1,
                    "dataHoraAgend": "2017-09-27T17:20:12.513"
                })
                this.showResposta()
                console.log(response.body)
             })
             .catch((error) => {
                /*swal({   title: `Falha ao enviar sua solicitação`,
                        html: `<strong>É importante verificar se todos os campos estão preenchidos, caso contrário contate o admin</strong>`,   
                        type: "error",  
                    })*/
                //=>CAPTURAR O RETORNO DO SERVIDOR NA MENSAGEM
                this.err = JSON.stringify(response.json)
                swal({
                  html: '<strong>' + this.err + '</strong>',
                  confirmButtonText:
                    '<i class="fa fa-thumbs-up"></i> Ok!',
                }) 
                console.log(response.json)
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
