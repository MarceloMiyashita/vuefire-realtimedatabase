<template>
  <div id="app" class="container">
    <div class="page-header">
        <h1>Produto-App</h1>
    </div>
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3>Adicionar Produto</h3>
      </div>
      <div class="panel-body">
        <form id="form" class="form-group" v-on:submit.prevent="addProduto">
                   <div class="form-group">
            <label for="prodDataAtual">DataAtual:</label>
             {{moment(newProduto.dataAtual).format("DD/MM/YYYY")}}
          </div>

           <div class="form-group">
            <label for="prodCodigo">Código:</label>
            <input type="text" placeholder="Ex: 123456" id="prodCodigo" class="form-control" v-model="newProduto.codigo">
          </div>

          <div class="form-group">
            <label for="prodDescricao">Descrição:</label>
            <input type="text" placeholder="Ex: farinha de trigo" id="prodDescricao" class="form-control" v-model="newProduto.descricao">
          </div>

          <div class="form-group">
            <label for="prodValidade">Data de Validade:</label>
            <input type="date" required="true" id="prodValidade" class="form-control" v-model="newProduto.validade">
          </div>

          <div class="form-group">
            <label for="prodLoja">Loja:</label>
            <input type="text" placeholder="Ex: Carrefour-SP" id="prodLoja" class="form-control" v-model="newProduto.loja">
          </div>

          <div class="form-group">
            <label for="prodTipo">Tipo:</label>
            <input type="text" placeholder="Ex: perecivel/nao-perecivel" id="prodTipo" class="form-control" v-model="newProduto.tipo">
          </div>
          <input type="hidden" id="produtoKey">
            <input type="submit" class="btn btn-success" value="Adicionar">
            <input type="button" id="btnUp" class="btn btn-info" value="Atualizar" v-on:click="updatebtn()" disabled>
        </form>
      </div>
    </div>
    <div class="panel panel-info">
      <div class="panel-heading">
        <h3>Listagem de produtos</h3>
      </div>
      <div class="panel-body">
        <table class="table table-striped">
          <thead class="thead-inverse">
            <tr>
              <th>Descrição</th>
              <th>Validade</th>
              <th>Editar</th>
              <th>Apagar</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="produto in produtos">
              <td>
                <a v-bind:href="produto.descricao">{{ produto.descricao }}</a>
              </td>
              <td>{{moment(produto.validade).format("DD/MM/YYYY")}}</td>
              <td>
                <span class="glyphicon glyphicon-pencil" v-on:click="updateProduto(produto)"></span>
              </td>
              <td>
                <span class="glyphicon glyphicon-trash" v-on:click="removeProduto(produto)"></span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
<script>


import Firebase from 'firebase'
import moment from 'moment'
import toastr from 'toastr';



let config = {
       apiKey: "AIzaSyCm3heuEhI7XUFNV0rXreI_kHO1exUHeVI",
    authDomain: "tikaraapp.firebaseapp.com",
    databaseURL: "https://tikaraapp.firebaseio.com",
    projectId: "tikaraapp",
    storageBucket: "tikaraapp.appspot.com",
    messagingSenderId: "9428422433"
}

let app = Firebase.initializeApp(config);
let db = app.database();
let produtosRef = db.ref('tikaraapp');
export default {
  name: 'app',
  firebase:{
    produtos: produtosRef
  },
  data (){
    return {
      newProduto: {
      
        dataAtual:Date.now(),
        codigo:'',
        descricao:'',
        loja:'',
        tipo: '',
        validade: null,
        

      }
    }
  },


  methods:{
    moment,
    addProduto: function () {
      produtosRef.push(this.newProduto);
      this.newProduto.dataAtual ='';
      this.newProduto.codigo = '';
      this.newProduto.descricao ='';
      this.newProduto.loja = '';
      this.newProduto.tipo ='';
      this.newProduto.validade = '';
    },
    updateProduto:function(produto) {
      this.newProduto.codigo = produto.codigo;
      this.newProduto.descricao = produto.descricao;
      this.newProduto.loja = produto.loja;
      this.newProduto.tipo = produto.tipo;
      this.newProduto.validade = produto.validade;
      const btnUpdate = document.getElementById('btnUp');
      btnUpdate.disabled = false;
      const inHidden = document.getElementById('produtoKey');
      inHidden.value = produto['.key'];

    },
    updatebtn:function() {
      const btnUpdate = document.getElementById('btnUp');
      const getIdUpdate = document.getElementById('produtoKey');
      produtosRef.child(getIdUpdate.value).set(this.newProduto);
      this.newProduto.codigo = '';
      this.newProduto.descricao = '';
      this.newProduto.loja = '';
      this.newProduto.tipo = '';
       this.newProduto.validade = '';
      btnUpdate.disabled = true;
    },
    removeProduto: function (produto) {
      produtosRef.child(produto['.key']).remove();
      toastr.success("Produto removido");
    }
  }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
