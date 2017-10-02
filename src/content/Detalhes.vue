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
          
        </section>
      </div>
        
      <footer class="card-footer">
        <p class="card-footer-item">
        <a @click="responder" class="button is-primary">Responder</a>
        </p>
      </footer>
      
    </div> 
    <br>
      
    <h2>Tópico: {{ compromissos.titulo }}</h2>
    <br>
      
     <!-- responder --> 
      
     <!-- detalhes -->
    <div v-for="compromisso in compromissosDet">
    
        <!--<template v-if="compromisso.nivel.length==1">
        <div class="columns">
            <div class="column is-1">
            </div>
            <div class="column">
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
                        nivel:  {{compromisso.nivel}}
                        </div>
                    </div>  

                    <a class="button is-primary" @click.prevent="showResposta(compromisso.idCompDet)">Novo Detalhe</a>

                    
                </div>
                 fim box principal 
            </div>
            <div class="column is-1">
            </div>
        </div>
        </template>-->
        
        
        <!-- sub-resposta par -->
        
        <template v-if="compromisso.nivel%2==0">
            
            <div class="columns">
                <div class="column is-3">
                </div>
                <div class="column">
                    <div class="box" id="coment">
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
                            nivel:  {{compromisso.nivel}}
                        </div>
                        <div v-if="visivel" class="columns">
                            <a class="button is-primary" @click.prevent="showResposta()">Novo Detalhe</a>
                        </div>
                    </div>
                </div>
            </div>
            
            
        </template>
        
        <!-- sub-resposta impar -->
        <template v-else="compromisso.nivel%2==1">
            <div class="columns">
                <div class="column">
                    <div class="box" id="coment">
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
                            nivel:  {{compromisso.nivel}}
                        </div>
                        <div v-if="visivel" class="columns">
                            <a class="button is-primary" @click.prevent="showResposta(compromisso)">Novo Detalhe</a>
                        </div>
                    </div>
                </div>
                <div class="column is-3">
                </div>
            </div>
        
        </template>
        
    </div>
      
    <!-- MODAL -->    

    <div id="modal_compromisso" class="modal" :class="{'is-active':showModal}">
      <div class="modal-background"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Resposta: {{ compDet.nivel }}</p>
          <button class="delete" @click.prevent="showModal=false"></button>
        </header>
        <section class="modal-card-body">

                <textarea id="detalhes" class="textarea" v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                <br>
                <div class="columns">

                    <div class="column">
                        <label class="label">Data:</label>
                        <div class="select">
                          <date-picker :date="startTime" :option="option" :limit="limit" id="data"></date-picker>
                        </div>
                        <!-- <span>{{ startTime.time }}</span> -->
                    </div>
                    <div class="column">
                        <label class="label">Arquivo:</label>   
                        <div class="file has-name">
                            <input class="file-input is- primary" type="file" name="resume">  
                        </div>
                    </div> 

                </div>

        </section>
        <div class="box">
          <div class="level">
              <div class="level-left">
                  <div class="level-item">
                  <a class="button is-primary" @click.prevent="salvarSubDet()">Enviar</a>
                  </div>
              </div>
              <div class="level-left">
                  <div class="level-item">
              <a class="button" @click.prevent="showModal=false">Cancelar</a>
                </div>
              </div>
          </div>
       </div>
      </div>
    </div>
    <!-- fim modal -->
      
    <!-- RESPOSTA GERAL -->    
      <div id="resposta" class="box" style="margin-top: 20px;">
          <label class="label">Resposta:</label>
                <textarea @click="responder()" class="textarea" v-model.trim="compDet.detalhes" placeholder="Digite a sua resposta" style="width: 100%;"></textarea>
                <br>
                <div class="columns">
                        
                    <div class="column">
                        <label class="label">Data:</label>
                        <div class="select">
                          <date-picker :date="startTime" :option="option" :limit="limit"></date-picker>
                        </div>
                        <!-- <span>{{ startTime.time }}</span> -->
                    </div>
                    <div class="column is-7">
                        <label class="label">Arquivo:</label>
                        
                        <div class="file has-name">
                            <input class="file-input is- primary" type="file" name="resume">  
                        </div>
                    </div>
                    <!-- <div class="column">
                    <label class="label">Status</label>
                      <div class="select">
                          <select v-model="compDet.idStatus">
                              <option v-for="stat in status" :value="stat.idStatus">
                                {{ stat.nome }}
                              </option>
                          </select>
                      </div>
                      <!-- <span>{{ compDet.idStatus }}</span> --
                    </div> -->
                    <div class="column is-1">    
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
        title: 'Tópico',
        showModal: false,
        isLoading: false,
        selected: {},
        compromissos: [],
        compromissosDet: [],
        showModalNew: false,
        showModalForum: false,
        visivel: true,
        msg: '',
        compDet: {
            "detalhes": '',
            "idComp": this.$route.query.q,
            "idUsuario": 4,
            "idStatus": 1,
            "dataHoraAgend": '',
            "nivel": ''
        },
        status: [],  
        usuarios: [
          { text: 'KEL', value: 4}
        ],
        idResposta: '',
        ultimoDet: '',
          
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
      
      responder(){
        let x = 0
        let y = 999999
        window.scrollBy(x,y)
        this.ultimoDet = this.compromissosDet.slice(-1)[0]
        
      },
      btnSubDets(){
        
      },
      validar() {
        if (this.compDet.detalhes==null || this.compDet.detalhes=='') {
          swal(
            'Oopa...',
            'Por favor, escreva  a ocorrência',
            'error'
          )
          this.detalhes.focus();
          return false
        }
        if (this.startTime.time==null || this.startTime.time=='') {
          swal(
            'Oopa...',
            'Por favor, preencha a data e hora!',
            'error'
          )
          this.data.focus();
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
      showResposta(compromisso){
        this.showModal = true;
        this.compDet.nivel = compromisso.nivel
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
          t.compDet.nivel = t.ultimoDet.nivel
          t.compDet.nivel++
          
             this.$http.post(ENDPOINT + 'api/comp/novoDet', this.compDet)
             .then((response) => {
                this.$set('compDet',{
                    "detalhes": '',
                    "idComp": this.$route.query.q,
                    "idUsuario": 4,
                    "idStatus": 1,
                    "nivel": this.compDet.nivel,
                    "dataHoraAgend": ''
                })
                this.$set('showModal',false)
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
      
      },
      salvarSubDet(){
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
                    "nivel": this.compDet.nivel,
                    "dataHoraAgend": ''
                })
                this.$set('showModal',false)
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
