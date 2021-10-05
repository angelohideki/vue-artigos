<template>
  <div class="hello container">
    <form>
      <div class="mb-3">
        <p>
          <input 
            type="text" 
            placeholder="Insira o título" 
            class="form-control" 
            v-model="artigo.titulo">
        </p>
        <p>
          <textarea 
            class="form-control" 
            placeholder="Insira seu conteúdo" 
            v-model="artigo.conteudo">
          </textarea>
        </p>
        <input type="hidden" v-model="artigo.id">
        <p>
          <button 
            v-if="editar"
            class="primary" 
            @click="cadastrarArtigos">
            Editar
          </button>
          <button 
            v-else 
            class="btn btn-success" 
            @click="cadastrarArtigos">
            Cadastrar
          </button>
        </p>
      </div>
    </form>

    <div class="card my-2">
      <div class="post" v-for="artigo in artigos" v-bind:key="artigo.id">
        <h3>{{ artigo.titulo }}</h3>
        <p>{{ artigo.conteudo }}</p>
        <hr>
        <p>
          <button class="btn btn-danger" @click="deletarArtigo( artigo.id )">Deletar</button> | 
          <button class="btn btn-info" @click="editarArtigo( artigo )">Editar</button>
        </p>
      </div>
    </div>

    <br><hr>

    <nav aria-label="Page navigation example">
      <ul class="pagination">

        <li class="page-item">
        <a 
          class="page-link" 
          v-bind:class="[{disabled: !paginacao.prev }]" 
          @click="fetchArtigos( paginacao.prev )">
          Anterior 
        </a>
        </li>

        <li class="page-item">
        <a 
          class="page-link page-ok"> 
          Página {{ paginacao.current_page }} de {{ paginacao.last_page }} 
        </a>
        </li>
        <li class="page-item">
        <a 
          class="page-link" 
          v-bind:class="[{disabled: !paginacao.next }]" 
          @click="fetchArtigos( paginacao.next )"> 
          Próxima
        </a>
        </li>

      </ul>
    </nav>

  </div>
</template>

<script>
export default {
  name: 'Artigos',
  data(){
    return {
      artigos: [],
      artigo: {
        id: '',
        titulo: '',
        conteudo: ''
      },
      editar: false,
      paginacao: {}
    }
  },

  methods: {
    fetchArtigos( url ){
      let virtual = this
      url = url || 'http://localhost:8000/api/artigos'
      fetch( url )
        .then( res => res.json())
        .then( res => {
          this.artigos = res.data
          virtual.paginar( res.meta, res.links )
        })
        .catch( err => console.log(err))
    },

    paginar( meta, links ){
      let paginacao = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next: links.next,
        prev: links.prev
      }

      this.paginacao = paginacao

    },

    cadastrarArtigos(){

      if( this.editar === false ){
        if( this.artigo.titulo == ''){
          alert('Preencha o título')
          return
        }
        fetch('http://localhost:8000/api/artigo', {
            method: 'post',
            body: JSON.stringify( this.artigo ),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then( res => res.json() )
          .catch( err => console.log( err ))
          alert('Dados cadastrados com sucesso!')
          this.fetchArtigos()
          this.artigo.id = ''
          this.artigo.titulo = ''
          this.artigo.conteudo = ''
      }else{
        fetch(`http://localhost:8000/api/artigo/${this.artigo.id}`, {
            method: 'put',
            body: JSON.stringify( this.artigo ),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then( res => res.json() )
          .catch( err => console.log( err ))
          alert('Dados Atualizados com sucesso!')
          this.fetchArtigos()
          this.artigo.id = ''
          this.artigo.titulo = ''
          this.artigo.conteudo = ''
      }
    },

    deletarArtigo( id ){
      if( confirm('Deseja deletar o artigo de id: ' + id) ){
        fetch(`http://localhost:8000/api/artigo/${id}`, {
          method: 'delete'
        })
        .then( res => res.json())
        .catch( err => console.log( err ))
        alert('O artigo de id: ' + id + ' foi deletado.')
        this.fetchArtigos()
      }
    },

    editarArtigo( artigo ){
      this.editar = true
      this.artigo.id = artigo.id
      this.artigo.titulo = artigo.titulo
      this.artigo.conteudo = artigo.conteudo
    }
  },

  created() {
    this.fetchArtigos()  
  }
}
</script>

<style scoped>
.hello {
  color: #333;
  padding: 20px;
  max-width: 1200px;
  margin: auto;
}

.card {
  background-color: #fff;
  color: #333;
}

.post {
  border-bottom:1px solid silver;
  padding: 10px;
  margin-top: 10px;
  text-align: left;
}

h3 {
  border-bottom: 1px dotted silver;
  margin-bottom: 10px;
}

.disabled {
  text-decoration: none;
  color: #ccc;
  cursor: default;
  pointer-events: none;
}

i { color: #333;}
.danger { color: #fff; background: red; padding: 5px; margin-top: 5px; }
.info { color: #fff; background: blue; padding: 5px; margin-top: 5px; }
.success { color: #fff; background: green; padding: 15px; border:1px solid #333; }
.primary { color: #333; background: cyan; padding: 15px; border:1px solid #333; }

.entrada {
  width: 98%;
  border: 1px solid #000;
  margin-top: 10px;
  padding: 10px;
  font-size: 18px;
  font-weight: bold;
}

input.entrada { height: 90px; }
textarea.entrada { height: 80px; margin-bottom: 10px;}

.page-link { cursor: pointer; }
.page-ok { cursor: auto; color: #333;}
</style>
