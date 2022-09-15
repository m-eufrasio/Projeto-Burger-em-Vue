<template>
    <Banner/>
    <div>
        <h1>Componente de Mensagem</h1>
        <div>
            <form id="burger-form">
                <div class="input-container">
                    <Label for="nome">Nome do Cliente:</Label>
                    <input type="text" id="nome" v-model="nome" placeholder="Digite o seu nome">
                </div>
            </form>
        </div>
        <div>
            <form id="burger-form">
                <div class="input-container">
                    <Label for="pao">Escolha o pão:</Label>
                    <select name="pao" id="pao" v-model="pao">
                        <option value="">Selecione o seu pão</option>
                        <!-- Para preencher os options, é necessário usar o v-for. -->
                        <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select>
                </div>
                <div class="input-container">
                    <Label for="carne">Escolha a carne:</Label>
                    <select name="carne" id="carne" v-model="carne">
                        <option value="">Selecione o tipo de carne</option>
                        <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select>
                </div>
            </form>
        </div>
        <div>
            <!-- @submit ira acionar o evento. -->
            <form id="burger-form" @submit="createBurger">
                <div class="input-container">
                    <Label for="opcionais-title">Selecione os opcionais:</Label>
                    <!-- Vamos repetir nessa estrutura, logo o v-for irá nesta div. -->
                    <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
                        <!-- O name é no plural pois estamos usando um array.  -->
                        <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                        <span>{{ opcional.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu burger" >
                </div>
            </form>
        </div>
    </div>
</template>

<script>
    export default {
    name: "BurgerForm",
    data() {
        return {
            // Os primeiros são no plural, pois estão vindo do servidor. 
            paes: null,
            carnes: null,
            opcionaisdata: null,
            // Os segundos, são no singular pois serão escolhas do usuário.
            nome: null,
            pao: null,
            carne: null,
            opcionais: [],
            msg: null,

        }
    },
    methods: {
        // Método assincrono para pegar os ingredientes do back.
        async getIngredientes() {
            // É uma requisição para essa URL que da acesso a API.
            // /ingredientes da acesso aos itens cadastrados no db.json.
            const req = await fetch("http://localhost:3000/ingredientes")
            const data = await req.json(); // Aqui espera uma req json.

            this.paes = data.paes;
            this.carnes = data.carnes;
            this.opcionaisdata = data.opcionais;

        },
        // Método para enviar os formulários. É inserido um argumento para parar
        // o evento de formulário quando clicar no submit.
        async createBurger(e) {
            e.preventDefault();
            
            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                // Esse dado precisa ir como array, logo usa-se o Array.from.
                opcionais: Array.from(this.opcionais),
                status: "Solicitado"
            }
            // É preciso enviar uma comunicação via request para o server.
            const dataJson = JSON.stringify(data);
            // Com os dados, teremos que formar o request.
            const req = await fetch("http://localhost:3000/burgers",{
                // Será necessário configurar algumas coisas. Se enviar sem elas será como um GET padrão.
                method: "POST",
                // No headers informa que está comunicando com JSON.
                headers: {"Content-Type": "application/json"},
                // Envia a mensagem, os dados em JSON.
                body: dataJson
            });

            // Variável para receber a resposta e se quiser, imprimir no console.
            const res = await req.json();
            // console.log(res);

            // Colocar uma msg no sistema

            // Limpar msg

            // Limpar os campos:
            this.nome = "";
            this.carne = "";
            this.pao = "";
            this.opcionais = "";
        }
    },
    mounted() {
        this.getIngredientes();
    }
}
</script>

<style scoped>

#burger-form {
    max-width: 400px;
    margin: 0 auto;
}

.input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
}

label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;
    padding: 5px 10px;
    border-left: 4px solid #FCBA03;
}

input, select {
    padding: 5px 10px;
    width: 300px;
}

#opicionais-container {
    flex-direction: row;
    flex-wrap: wrap
}

#opicionais-title {
    width: 100%;
}

.checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
    width: auto;
}

.checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
}

.submit-btn {
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

.submit-btn:hover {
    background-color: #fcba03;
    color: #222;
}

</style>