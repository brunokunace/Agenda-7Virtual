<template>
    
  <h1>Teste Select</h1>
  <ol>
    <li v-for="tipo in tipos">
      <p>{{tipo.nome}}</p>
    </li>
  </ol>
    
    <br><br><br>
    
    <select v-model="selected">
      <option v-for="tipo in tipos">
        {{ tipo.nome }}
      </option>
    </select>
    <br><br>
    <span>Selecionado: {{ selected }}</span>
    
    <hr>
    
    <!-- CONTINUE ABAIXO  depois da tag <hr>-->
    <div class="file">
      <label class="file-label">
        <input class="file-input" type="file" name="resume">
        <span class="file-cta">
          <span class="file-icon">
            <i class="fa fa-upload"></i>
          </span>
          <span class="file-label">
            Choose a file…
          </span>
        </span>
      </label>
    </div>
    
    <hr>
    
    
    
</template>

<script>
import axios from 'axios'
const ENDPOINT = 'http://192.168.0.200/helpdesk/api/comp/obterCompTipo'

export default {
  data: () => ({
    selected: '',
    tipos: [],
    
  }),
  // Fetches posts when the component is created.
  created() {
    axios.get(ENDPOINT)
    .then(response => {
      // JSON responses are automatically parsed.
      this.tipos = response.data
      console.log()
    })
    .catch(e => {
      this.errors.push(e)
    })
  }
}
</script>