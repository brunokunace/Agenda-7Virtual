<template>
   <tr v-for="compromisso in compromissos">
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.idComp}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.titulo}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.tipoComp}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.status}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.numPrioridade}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.projeto}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.plataforma}}</td>
                <td v-link="{ path: '/cdetalhe' }">{{compromisso.usuario}}</td>
                <!-- <td class="is-icon">
                 <a href="#" @click.prevent="obsCompr(compromisso)">
                    <i class="fa fa-eye"></i>
                  </a>
                  <a href="#" @click.prevent="editarCompromisso(compromisso)">
                    <i class="fa fa-edit"></i>
                  </a>
                  <a href="#" @click.prevent="removerCompromisso(compromisso)">
                    <i class="fa fa-trash"></i>
                  </a>
                </td> -->
              </tr>
</template>

<script>
export default {
    name: 'Compromissos',
    data () {
      return {
        isLoading: false,
        title: 'Compromissos',
        search: '',
        compromissos: [],
        page: 1,
        total: 0,
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
        ]
      }
    },
    methods: {
      loadCompromissos(){

        let t = this
        this.showLoading()

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

       }
    }
}
</script>