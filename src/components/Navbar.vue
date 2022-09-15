<template>
    <div id="nav">
        <!-- Aqui o to irá mandar para o home. -->
        <router-link to="/" id="logo-url">
            <!-- O src dessa img vai vir por (entendi proxy, mas n sei oq é). -->
            <img :src="logo" :alt="alt" id="logo">
        </router-link>
        <!-- Esse 'to' define qual rota esse router-link irá representar. -->
        <router-link to="/">Home</router-link>
        <router-link to="/pedidos">Pedidos</router-link>
    </div>
</template>

<script>
    export default {
    name: "Navbar",
    // Lá no HTML, será inserido os dois pontos : no src para receber a logo
    // e no alt com a descrição.
    props: ["logo", "alt"]
    }
</script>

<style scoped>
    #nav {
      background-color: #222;
      border-bottom: 4px solid #111;
      padding: 15px 40px;
      display: flex;
      justify-content: flex-end;
      align-items: center;
    }
    
    #nav #logo-url {
      margin: auto;
      margin-left: 0;
    }

    #logo {
        width: 40px;
        height: 40px;
    }

    #nav a {
        color: #FCBA03;
        text-decoration: none;
        margin: 12px;
        transition: .5s;
    }

    #nav a:hover {
        color: #FFF;
    }
    </style>