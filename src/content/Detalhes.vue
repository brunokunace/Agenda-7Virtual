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
              <label class="label">Data/Hora:</label>
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
        
      <footer class="card-footer">
        <p class="card-footer-item">
        <a href="#resposta" class="button is-primary">Responder</a>
        </p>
      </footer>
      
    </div>
      
    <br>
      
    <h2>Tópico: {{ compromissos.titulo }}</h2>
    <br>
      
     <!-- responder --> 
      
      <!-- detalhes -->
    <div v-for="compromisso in compromissosDet">
    
        <div class="box">
            <div class="columns">
                <div class="column is-2">
                    <strong>cód:</strong>
                    <strong>{{compromisso.idCompDet}}</strong> 
                    
                </div>
                <div class="column is-5">
                    <strong>Data/Hora de Abertura:</strong>
                    {{compromisso.dataHoraAgend}}
                </div>    
                <div class="column is-5">
                    <strong>Data/Hora de Atendimento:</strong>
                    {{compromisso.dataHoraAtend}}
                </div>
            </div>
            
            <hr style="margin-top: 5px;">
            
            <div class="columns">
                <div class="column">
                <label class="label">Mensagem:</label>
                <div style="font-size: 30px;">{{compromisso.detalhes}}</div>
                </div>
            </div>
            <div class="columns">
                
            </div>  
            
            <a class="button is-primary" @click="showResposta">Novo Detalhe</a>
            <span v-if="visivel">
                <br><br>
                <textarea class="textarea" v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                <br>
                <div class="columns">
                        
                    <div class="column">
                        <label class="label">Data:</label>
                        <div class="select">
                          <date-picker :date="startTime" :option="option" :limit="limit"></date-picker>
                        </div>
                        <!-- <span>{{ startTime.time }}</span> -->
                    </div>
                    <div class="column">
                    <label class="label">Status</label>
                      <div class="select">
                          <select v-model="compDet.idStatus">
                              <option v-for="stat in status" :value="stat.idStatus">
                                {{ stat.nome }}
                              </option>
                          </select>
                      </div>
                      <!-- <span>{{ compDet.idStatus }}</span> -->
                    </div>
                    <div class="column is-2">    
                        <a class="button is-primary enviar" @click.prevent="salvarDet()">Enviar</a>
                    </div>
                </div>
            </span>  
    
            
          </div><br>          
   </div>  
      <div id="resposta" class="box">
                <textarea class="textarea" v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                <br>
                <div class="columns">
                        
                    <div class="column">
                        <label class="label">Data:</label>
                        <div class="select">
                          <date-picker :date="startTime" :option="option" :limit="limit"></date-picker>
                        </div>
                        <!-- <span>{{ startTime.time }}</span> -->
                    </div>
                    <div class="column">
                    <label class="label">Status</label>
                      <div class="select">
                          <select v-model="compDet.idStatus">
                              <option v-for="stat in status" :value="stat.idStatus">
                                {{ stat.nome }}
                              </option>
                          </select>
                      </div>
                      <!-- <span>{{ compDet.idStatus }}</span> -->
                    </div>
                    <div class="column is-2">    
                        <a class="button is-primary enviar" @click.prevent="salvarDet()">Enviar</a>
                    </div>
                </div>
     </div> 
 </div>   
</template>

<script>

import axios from 'axios'
import myDatepicker from 'vue-datepicker'
    
  //produção:
  const ENDPOINT = 'http://192.168.0.200/helpdesk/'
  
  // ao descomentar abaixo tem que comentar a const acima
  //debug:
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
        compromissos: [],
        compromissosDet: [],
        showModalNew: false,
        showModalForum: false,
        visivel: false,
        msg: '',
        compDet: {
            "detalhes": '',
            "idComp": this.$route.query.q,
            "idUsuario": 4,
            "idStatus": 1,
            "dataHoraAgend": '',
            "nivel": 1,
        },
        status: [],  
        usuarios: [
          { text: 'KEL', value: 4}
        ],
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
            format: 'YYYY-MM-DD' + moment().format('THH:mm'),
            placeholder: 'Insira a data',
            inputStyle: {
              'display': 'inline-block',
              'padding': '6px',
              'line-height': '18px',
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
          type: 'time',
          week: ['Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sab', 'Dom'],
          month: ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'],
          format: 'YYYY-MM-DDTHH:mm'
        },
        multiOption: {
          type: 'multi-day',
          week: ['Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sab', 'Dom'],
          month: ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'],
          format: 'YYYY-MM-DDTHH:mm'
        },
        limit: [{
          type: '', // preencher com 'weekday' para impor limite no calendário e ocultar dom/sab
          available: [1, 2, 3, 4, 5, 6] //define o limite de dias uteis - [6] inclui o sábado no calendário  
        },
        { // sem uso no momento
          type: 'fromto',
          from: '',
          to: ''
        }
      ]

        }
    },
    components: {
        'date-picker': myDatepicker
    },
    
     // METODOS ======================================
    
    methods: {
        
      validar() {
        if (this.compDet.detalhes==null || this.compDet.detalhes=='') {
          swal(
            'Oopa...',
            'Por favor, escreva  a ocorrência',
            'error'
          )
          this.selected.idCompTipo.focus();
          return false
        }
        if (this.startTime.time==null || this.startTime.time=='') {
          swal(
            'Oopa...',
            'Por favor, preencha a data e hora!',
            'error'
          )
          this.selected.idCompTipo.focus();
          return false
        }
      },
      selectStatus(){
        axios.get(ENDPOINT + 'api/comp/obterStatus')
        .then(response => {
          
          this.status = response.data
          console.log()
        })
        .catch(e => {
          this.errors.push(e)
        })
      },
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

        let q = t.compDet.idComp;

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

        let q = t.compDet.idComp;
        
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
          this.validar()
          
          let t = this
          
          t.compDet.dataHoraAgend = t.startTime.time
          t.compDet.idUsuario = 4
          
          
             this.$http.post(ENDPOINT + 'api/comp/novoDet', this.compDet)
             .then((response) => {
                this.$set('compDet',{
                    "detalhes": '',
                    "idComp": this.$route.query.q,
                    "idUsuario": 4,
                    "idStatus": 1,
                    "nivel": 1,
                    "dataHoraAgend": ''
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
                /*this.err = JSON.stringify(response.json)
                swal({
                  html: '<strong>' + this.err + '</strong>',
                  confirmButtonText:
                    '<i class="fa fa-thumbs-up"></i> Ok!',
                }) */
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
      t.selectStatus()
      
    }
}
</script>

<style scoped>
    h2 {
        font-size: 35px;
    }
    .enviar {
        margin-top: 25px;
    }
</style>
