<template>
  <i class="fixo fa fa-spinner fa-pulse fa-5x fa-fw" v-show="isLoading"></i>
  <span class="fixo sr-only" v-show="isLoading">Carregando...</span>
  <div class="container">
      <!-- cabeçario -->
    <div class="card" style="width: 100%;">
      <header class="card-header">
        <p class="card-header-title">
          Compromisso
        </p>
      </header>
      <div class="card-content">
          
      
        <section>
            
          <div class="columns">
              
            <div class="column">
              <label class="label">Data:</label>
              <div v-show="compromissos.dataHora"></div>
            </div>
              
            <div class="column">
              <label class="label">Tipo:</label>
              
            </div>
              
            <div class="column is-4">
              <label class="label">Status:</label>
              
            </div>
              
          </div>
          
          <div class="columns">
            <div class="column">
              <label class="label">Título:</label>

            </div>
            <div class="column is-4">
              <label class="label">Projeto:</label>

            </div>
          </div>
            
        
          <div class="columns">
            <div class="column">
              <label class="label">Plataforma:</label>
              
            </div>
            <div class="column">
              <label class="label">Prioridade:</label>
              
            </div>
            <div class="column">
              <label class="label">Usuário:</label>
              
            </div> 
          </div>
          
          <br>
          
        </section>
      </div>
      <!--<footer class="card-footer">
        <a href="#" class="card-footer-item">Save</a>
        <a href="#" class="card-footer-item">Edit</a>
        <a href="#" class="card-footer-item">Delete</a>
      </footer>-->
    </div>
      
    <br>
      
    <h2>{{ title }}</h2>
      <!-- detalhes -->
      
    <table class="table is-narrow is-bordered is-mobile">
            <thead>
                <th>Cód</th>
                <th>Abertura</th>
                <th>Data/Hora Atendimento</th>
                <th>Usuário</th>
                <th>Mensagem</th>
                <th>Ações</th>
                

            </thead>
            <tbody>
              <tr v-for="compromisso in compromissosDet">
                <td>{{compromisso.idComp}}</td>
                <td>{{compromisso.dataHoraAgend}}</td>
                <td>{{compromisso.dataHoraAtend}}</td>
                <td>{{compromisso.idUsuario}}</td>
                <td><strong>{{compromisso.detalhes}}</strong></td>
                <td class="is-icon">
                 <a href="#" @click.prevent="obsCompr(compromisso)">
                    <i class="fa fa-eye"></i>
                  </a>
                  <a href="#" @click.prevent="editarCompromisso(compromisso)">
                    <i class="fa fa-plus"></i>
                  </a>
                 <br> 
                </td>
              </tr>
            </tbody>
      </table>
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
        prioridades: [
          { text: 'BAIXA', value: 1 },
          { text: 'MÉDIA', value: 2 },
          { text: 'ALTA', value: 3 },
          { text: 'PRA ONTEM!', value: 4 }
        ],
        plataformas: [
          { text: 'DESKTOP', value: 1},
          { text: 'WEB', value: 2 },
          { text: 'MOBILE', value: 3 }
        ],
        usuarios: [
          { text: 'KEL', value: 4}
        ],
        projetos: [
          { text: 'PROJETO INICIAL', value: 3 }
        ],
      }
    },
    methods: {
      showLoading(){
        this.isLoading=true;
      },
      hideLoading(){
        this.isLoading=false;
      },
      loadCompromissos(){

        let t = this
        this.showLoading()

        let qString = 13;

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(ENDPOINT + `api/comp/obterComp?idComp=${qString}`).then(
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

        let qString = 450;

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(ENDPOINT + `api/comp/obterCompDet?idComp=${qString}`).then(
         response=>{
           t.compromissosDet = response.json()
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

       
       },
       obsCompr(compromisso) {
            swal({
              title: 'Mensagens',
              type: 'info',
              html: '<p style="font-size:20px">' + `${compromisso.detalhes}` + '</p>',
              showCloseButton: true,
              confirmButtonText:
                '<i class="fa fa-thumbs-up"></i> Ok!',
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
