<template>
  <div id="burger-table">
    <Message :msg="msg" v-show="msg" />
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <!-- Essa parte é referente a edição dos dados. -->
        <div>Ações:</div>
      </div>
    </div>
    <div id="burger-table-rows">
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul style="list-style-type: none;">
            <!-- Nos opicionais não se tem ID então é necessário atribuir index neles -->
            <li v-for="(opcional, index) in burger.opcionais" :key="index">
              {{ opcional }}
            </li>
          </ul>
        </div>
        <div>
          <!-- Quando houve alteração, pelo evento do change será -->
          <select name="status" id="status" @change="updateBurger($event, burger.id)">
            <option value="">Selecione</option>
            <!-- Aqui resgata os status do db.json de forma dinâmica. -->
            <!-- Selected será bindado para ficar dinâmico. -->
            <!-- Igualando o status com o tipo, faz com que todos estejam
              como 'solicitado' pois quando é registrado o burger na table
              ele é considerado como um pedido. -->
            <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Message from "./Message.vue";

  export default {
    name: "Dashboard",
    data() {
      return {
        // Quando o usuário entrar vai ativar o método carregando no back
        // trazendo os hamburgers.
        burgers: null,
        burger_id: null,
        status: [],
        // Essa msg será preenchida quando o pedido for realizado.
        msg: null
      }
    },
    components: {
      Message
    },
    methods: {
      async getPedidos() {
        // Uma requisição assíncrona que irá esperar até os dados serem dados
        // depois irá acessar a rota de burgers pois lá será exibido os dados
        // que estamos buscando (os pedidos em si).
        const req = await fetch("http://localhost:3000/burgers");
        // Aqui estamos dizendo que as reqs são do tipo JSON.
        const data = await req.json();
        // Os dados que vieram da req estão sendo colocados no data() no
        // lugar de null do objeto burgers.
        this.burgers = data;
        // Resgatar os status
        this.getStatus();
      },
      async getStatus() {
        const req = await fetch("http://localhost:3000/status");
        const data = await req.json();
        this.status = data;
      },
      async deleteBurger(id) {
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
          method: "DELETE"
        });

        const res = await req.json();

        this.msg = "Pedido removido com sucesso!";

        // Limpar msg, ele esvazia a string após 3s.
        setTimeout(() => this.msg = "", 3000);

        // Chamando os pedidos novamente força uma atualização no sistema
        // por meio do backend.
        this.getPedidos();
      },
      async updateBurger(event, id) {
        // Dessa forma pegamos a opção que o usuário selecionou.
        const option = event.target.value;
        // Aqui colocamos o status em string.
        const dataJson = JSON.stringify({ status: option });
        // Aqui faremos a req:
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
          // O PATCH funciona como o update, mas ele atualiza o que enviamos.
          method: "PATCH",
          headers: { "content-type": "application/json" }, // Aqui estamos mudando o content-type.
          body: dataJson
      });

        const res = await req.json();

        this.msg = `O pedido N° ${res.id} foi atualizado para o status ${res.status}`

        // Limpar msg, ele esvazia a string após 3s.
        setTimeout(() => this.msg = "", 3000);
      }
    },
    mounted() {
      this.getPedidos();
    }
  }
</script>

<style scoped>
  #burger-table {
    max-width: 1200px;
    margin: 0 auto;
  }

  #burger-table-heading,
  #burger-table-rows,
  .burger-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #burger-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  #burger-table-heading div,
  .burger-table-row div {
    width: 19%;
  }

  .burger-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #ccc;
  }

  #burger-tabe-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn {
    background-color: #222;
    color: #fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .delete-btn:hover {
    background-color: #fcba03;
    color: #222;
  
  }
  
</style>