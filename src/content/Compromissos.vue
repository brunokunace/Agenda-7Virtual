<template>
  <i class="fixo fa fa-spinner fa-pulse fa-5x fa-fw" v-show="isLoading"></i>
  <span class="fixo sr-only" v-show="isLoading">Carregando...</span>
  
    
  <div class="container">
    <h1 class="title">{{title}}</h1>
    <div class="columns">
      <!-- CAMPO DE BUSCAR
      <div class="column is-5">
        <p class="control has-addons">
          <input class="input is-expanded" type="text" placeholder="O que você procura?" v-model="search" v-on:keyup.enter="searchCompromissos">
          <a class="button is-info" @click.prevent="searchCompromissos"><i class="fa fa-search"></i></a>
        </p>
      </div> -->
      
      <div class="column is-1">
        <a class="button is-info" @click.prevent="newCompromissos">Novo Compromisso</a>
      </div>



    </div>
    <div class="columns">
      <div class="column is-12">
        <table class="table is-narrow is-bordered is-mobile">
              <thead>
                <th>Cód</th>
                <th>Titulo</th>
                <th>Tipo</th>
                <th>Status</th>
                <th>N. Prioridade</th>
                <th>Projeto</th>
                <th>Plataforma</th>
                <th>Usuário</th>
                <!-- <th>Ações</th> -->

            </thead>
            <tbody>
              <tr v-for="compromisso in compromissos">
               
                <td @click="filtro = compromisso.idComp" v-link="{ path: '/cdetalhe', query: {q:filtro}}">
                    {{compromisso.idComp}}
                </td>
                
                <td>{{compromisso.titulo}}</td>
                <td>{{compromisso.tipoComp}}</td>
                <td>{{compromisso.status}}</td>
                <td>{{compromisso.numPrioridade}}</td>
                <td>{{compromisso.projeto}}</td>
                <td>{{compromisso.plataforma}}</td>
                <td>{{compromisso.usuario}}</td>
                <!-- <td class="is-icon">
                  <a href="#" @click.prevent="editarCompromisso(compromisso)">
                    <i class="fa fa-edit"></i>
                  </a>
                  <a href="#" @click.prevent="removerCompromisso(compromisso)">
                    <i class="fa fa-trash"></i>
                  </a>
                </td> -->
              </tr>
            </tbody>
        </table>
      
      <!-- Paginação -- 
      <Pagination :total="total" :page="page" :itens-per-page="itensPerPage" @change-page="onChangePage"></Pagination> -->
      </div>
    </div>
</div>
    
    <!-- novo compromisso -->
    <div id="modal_compromisso" class="modal" :class="{'is-active':showModalNew}">
      <div class="modal-background"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Compromisso: {{selected.id}}</p>
          <button class="delete" @click.prevent="showModalNew=false"></button>
        </header>
        <section class="modal-card-body">
            
          <div class="columns">
              
            <div class="column">
              <label class="label">Data</label>
              <p v-text="currentTime"></p>
            </div>
            <div class="column">
              <label class="label">Hora</label>
              <p v-text="currentHour"></p>
            </div>
              
            <div class="column">
              <label class="label">Tipo</label>
              <div class="select">
                  <select v-model="comp.idCompTipo">
                      <option v-for="tipo in tipos" :value="tipo.idCompTipo">
                        {{ tipo.nome }}
                      </option>
                  </select>
                  
              </div>
            </div>
              
            <div class="column is-4">
              <label class="label">Status</label>
              <div class="select">
                  <select v-model="comp.idStatus">
                      <option v-for="stat in status" :value="stat.idStatus">
                        {{ stat.nome }}
                      </option>
                  </select>
                  
              </div>
            </div>
              
          </div>
          
          <div class="columns">
          <div class="column">
          <label class="label">Título</label>
            <p class="control">
              <input class="input" placeholder="Defina um nome para o projeto..." v-model="comp.titulo">
            </p>
          <br>
          </div>
          <div class="column is-4">
          <label class="label">Projeto</label>
            <div class="select">
              <select v-model="comp.idProjeto">
                  <option v-for="projeto in projetos" :value="projeto.idProjeto">
                    {{ projeto.nome }}
                  </option>
              </select>
            </div>
            <p></p>
          
              
              </div>
            </div>
          <div class="columns">
            <!--<div class="column">
              <label class="label">Plataforma</label>
              <div class="select">
                  <select><!-- v-model="selected.plataforma" --
                      <option v-for="plataforma in plataformas">
                        {{ plataforma.text }}
                      </option>
                  </select>
              </div>
            </div>-->
            <div class="column">
              <label class="label">Prioridade</label>
              <div class="select">
                  <select v-model="comp.numPrioridade">
                      <option v-for="prioridade in prioridades" :value="prioridade.value">
                        {{ prioridade.text }}
                      </option>
                  </select>
              </div>
            </div>
            <div class="column">
              <label class="label">Usuário</label>
              <div class="select">
                  <select v-model="comp.idUsuario">
                      <option v-for="usuario in usuarios" :value="usuario.value">
                        {{ usuario.text }}
                      </option>
                  </select>
              </div>
            </div>
            
          </div>
            
          <label class="label">Detalhe</label>
          
          <p class="control">
            <textarea class="textarea" placeholder="escreva o comentário..." v-model.trim="msg"></textarea>
          </p>
          
          <br>
          
        </section>
        <footer class="modal-card-foot">
          <a class="button is-primary" @click.prevent="salvarCompromisso">Salvar</a>
          <a class="button" @click.prevent="showModalNew=false">Cancelar</a>
        </footer>
      </div>
    </div>
    
    
</template>

<script>
  //plugin para requisições
  import axios from 'axios'
  
  //captura hora e data
  var moment = require('moment');
  require("moment/min/locales.min");
  moment.locale('pt-br');
    
  //produção:
  const ENDPOINT = 'http://192.168.0.200/helpdesk/'
  
  // ao descomentar abaixo tem que comentar a const acima
  //debug:
  // const ENDPOINT = 'http://192.168.0.115:32688/'

  export default {
    name: 'Compromissos',
    data () {
      return {
        q: '489',
        isLoading: false,
        title: 'Compromissos',
        search: '',
        compromissos: [],
        selected: {},
        showModalNew: false,
        showModalForum: false,
        currentTime: moment().format('L'),
        currentHour: moment().format('LT'),
        tipos: [],
        status: [],
        prioridades: [
          { text: 'BAIXA', value: 1 },
          { text: 'MÉDIA', value: 2 },
          { text: 'ALTA', value: 3 }
        ],
        plataformas: [
          { text: '', value: 0},
          { text: 'DESKTOP', value: 1},
          { text: 'WEB', value: 2 },
          { text: 'MOBILE', value: 3 }
        ],
        usuarios: [
          { text: 'KEL', value: 4}
        ],
        projetos: [],
        comp: {
              
              "idCompTipo": '',
              "idStatus": '',
              "idProjeto": '',
              "titulo": '',
              "numPrioridade": '',
              "idUsuario": 4,
              "compromissosDet": []          
        },
        msg: '',
        filtro: ''
      } 
    },
    
    methods: {
      
      validar() {
        
        if (this.selected.idCompTipo==null || this.selected.idCompTipo=='') {
          swal(
            'Oopa...',
            'Por favor, preencha o tipo do compromisso!',
            'error'
          )
          this.selected.idCompTipo.focus();
          return false
        }
        if (this.selected.idStatus==null || this.selected.idStatus=='') {
          swal(
            'Por favor, preencha o status!',
            'Ahh vamos lá, vamos fazer o serviço direito!',
            'error'
          )
          this.selected.idStatus.focus();
          return false
        }
        if (this.selected.titulo==null || this.selected.titulo=='') {
          swal(
            'Oopa...',
            'Por favor, preencha o tipo do compromisso!',
            'error'
          )
          this.selected.titulo.focus();
          return false
        }
        if (this.selected.numPrioridade==null || this.selected.numPrioridade=='') {
          swal(
            'Por favor, preencha a prioridade. É imprescindível!',
            'Se não preencher a prioridade, este compromisso não pode ser tratado da forma adequada',
            'error'
          )
          this.selected.numPrioridade.focus();
          return false
        }
      },
      selectTipo(){
        axios.get(ENDPOINT + 'api/comp/obterCompTipo')
        .then(response => {
          
          this.tipos = response.data
          console.log()
        })
        .catch(e => {
          this.errors.push(e)
        })
      },
      selectProjeto(){
        axios.get(ENDPOINT + 'api/projetos/todos')
        .then(response => {
          
          this.projetos = response.data
          console.log()
        })
        .catch(e => {
          this.errors.push(e)
        })
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
      onChangePage(page){
        this.page = page
        this.loadCompromissos()
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

        let start = (this.page * this.itensPerPage) - (this.itensPerPage)
        let end = this.page * this.itensPerPage
        let qString = "";

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(ENDPOINT + `api/comp/obterComp?${qString}`).then(
         response=>{
           t.compromissos = response.json()
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

       },
       searchCompromissos(){
        this.loadCompromissos()
       },
       newDetalhes(){
        this.selected={}
        this.showModalForum = true;
       },
       newCompromissos(){
        this.selected={}
        this.showModalNew = true;
       },
       editarCompromisso(compromisso){
        this.selected=compromisso
        this.showModalNew = true;
       },
       removerCompromisso(compromisso){
        let self = this;
        swal({   title: `Você tem certeza que deseja apagar o "${compromisso.titulo}"?`,
                 text: `Esta ação é irreversível!`,   
                 type: "warning",   
                 showCancelButton: true,   
                 confirmButtonColor: "#DD6B55",   
                 cancelButtonText: "Cancelar",
                 confirmButtonText: "Sim, pode apagar!", 
                 showLoaderOnConfirm: true,  
                 closeOnCancel: true }).then( 
                 function (value) {
                     if (value === false) {
                         return false; 
                     }
                      else{
                      self.$http.delete(ENDPOINT + `api/comp/obterComp/${compromisso.id}`).then(
                      result=>{
                        swal(
                            'Deletado!',
                            'Este cadastro foi excluido!',
                            'success'
                        )
                      self.loadCompromissos()
                      })
                    }
                 })
       },
       salvarCompromisso(){
        // this.validar()
        
        det = {detalhes: this.msg}
        this.comp.compromissosDet.push(det)
        
        
        /*if (this.selected.id!=null){  //EDITAR
          this.$http.put(ENDPOINT + `api/comp/obterComp/${this.selected.id}`,this.selected).then(
            response=>{
              this.$set('selected',{})
              this.$set('showModalNew',false)
            },
            error=>{
              console.error(error)
            }
            ).finally(
              this.loadCompromissos()
            )
          }
          else {*/ //NOVO
           this.$http.post(ENDPOINT + 'api/comp/novoCab',this.comp)
             .then((response) => {
                this.$set('comp',{
                  "idCompTipo": '',
                  "idStatus": '',
                  "idProjeto": '',
                  "titulo": '',
                  "numPrioridade": '',
                  "idUsuario": '',
                  "compromissosDet": []
                })
                this.$set('msg','')
                this.$set('showModalNew',false)
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
                this.loadCompromissos()
             })
          // <-- inserir uma chave aqui ao descomentar o if /*}*/
      },
      carregarComp(compromisso){
        this.filtro = compromisso.idComp
      }
    },
    created(){
      let t = this
      t.loadCompromissos()
      t.selectTipo()
      t.selectStatus()
      t.selectProjeto()
    },
  }
</script>
<style>
ul {
  padding: 0;
}

.user {
  height: 30px;
  line-height: 30px;
  padding: 10px;
  border-top: 1px solid #eee;
  overflow: hidden;
  transition: all .25s ease;
}

.user:last-child {
  border-bottom: 1px solid #eee;
}

.v-enter, .v-leave-active {
  height: 0;
  padding-top: 0;
  padding-bottom: 0;
  border-top-width: 0;
  border-bottom-width: 0;
}
.fixo{float: right; margin-right: 10px; margin-top: 0px; z-index: 1000;}
</style>