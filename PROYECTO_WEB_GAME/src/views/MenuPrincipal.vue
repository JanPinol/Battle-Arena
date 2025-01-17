<script>
import TopBar from "@/components/TopBar.vue";
import SideBar from "@/components/SideBar.vue";
import StorePopup from "@/views/StoreView.vue";
import OfertaEspecial from "@/views/OfertaEspecialView.vue";
import MovementBlock from "@/components/MovementBlock.vue";

export default {
  name: "MenuPrincipal",
  components: {
    MovementBlock,
    OfertaEspecial,
    StorePopup,
    TopBar,
    SideBar
  },
  data() {
    return {
      showStorePopup: false,
      showSpecialOfferPopup: false,
      isGameStarted: localStorage.getItem("inGame"),
      isMobile: window.innerWidth <= 700, // Inicializa según el ancho de la ventanaarena: {
      arena: {
        finished: false,
      }
    };
  },
  mounted() {
    this.getIfIsInArena();
  },
  created() {
    window.addEventListener('resize', this.handleResize);
    this.handleResize(); // Llama al inicio para establecer el estado inicial
  },
  unmounted() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    handleResize() {
      this.isMobile = window.innerWidth <= 700;
    },
    // Comprueba si da el error 403 dando a entender que esta en una partida
    getIfIsInArena() {
      fetch("https://balandrau.salle.url.edu/i3/players/arenas/current", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Bearer: localStorage.getItem("token"),
        },
      })
      .then((data) => {
        // Establecer los datos del juego recuperados en el estado del componente
        if (data && data[0]) {
          // Establecer los datos del juego recuperados en el estado del componente
          this.arena = data[0];
          // Verificar si el juego ha terminado
          if (!this.arena.finished) {
            this.isGameStarted = 'false';
          }
        }
      })
    }
  }
};
</script>

<template>
  <!-- Muestra TopBar en pantallas no móviles -->
  <TopBar v-if="!isMobile"></TopBar>
  <!-- Muestra SideBar en pantallas móviles -->
  <SideBar v-if="isMobile"></SideBar>

  <main class="main-grid">
    <!-- Primera Columna -->
    <section class="store-column card">
      <button class="CoolButton2" @click="showStorePopup = true">Store</button>

      <!-- Popup Store -->
      <div v-if="showStorePopup" class="popup">
        <StorePopup @close="showStorePopup = false"></StorePopup>
      </div>

      <!-- Botón para la oferta especial -->
      <button @click="showSpecialOfferPopup = true">
        <img src="/MainMenuImages/shop-image.png" alt="Oferta Especial" class="store-image">
      </button>

      <!-- Popup para la oferta especial -->
      <div v-if="showSpecialOfferPopup" class="popup">
        <OfertaEspecial @close="showSpecialOfferPopup = false"></OfertaEspecial>
      </div>
    </section>

    <!-- Segunda Columna -->
    <section class="arena-column">
      <router-link to="/CreateArenaView" class="CoolButton1">Create Arena</router-link>
      <!-- Comprueba si el usuario ya ha iniciado una partida -->
      <router-link v-if = "isGameStarted === 'true'" local to="/GameView" class = "CoolButton1">Current game</router-link>
      <router-link v-if = "isGameStarted === 'false'" to="/ListGames" class="CoolButton1">Join Arena</router-link>
      <router-link to="/ListadoJugadores" class="CoolButton1 SmallButton SmallButton1">Listado de Jugadores</router-link>
      <router-link to="/HistorialJugadores" class="CoolButton1 SmallButton">Historial de Jugadores</router-link>

      <MovementBlock class = "movement-block"></MovementBlock>
    </section>

    <!-- Tercera Columna -->
    <section class="backpack-column card">
      <router-link to="/BackpackView" class="CoolButton2">Backpack</router-link>
      <div class="current-attacks">
        <div class="attack-item">
          <span>Attack 1</span>
          <img src="/StorePopupImages/attack-image-1.png" alt="Attack 1">
        </div>
        <div class="attack-item">
          <span>Attack 2</span>
          <img src="/StorePopupImages/attack-image-2.png" alt="Attack 2">
        </div>
        <div class="attack-item">
          <span>Attack 3</span>
          <img src="/StorePopupImages/attack-image-4.png" alt="Attack 3">
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
  @import "../assets/menu.css";
  @import "../assets/Buttons/CoolButton1.css";
  @import "../assets/Buttons/CoolButton2.css";
  @import "../assets/Columns/CoolColumn.css";

  /*Estilos para los botones*/
  .CoolButton1 {
    width: 80%;
    text-align: center;
    display: block;
    margin: 5% auto;
    font-size: 3vw;
  }

  .CoolButton2 {
    width: 100%;
    text-align: center;
    font-size: 2vw;
    margin-bottom: 5%;
    border: none;
  }
  .SmallButton1 {
    margin-top: 20%;
    font-size: 0.9vw;
    width: 50%;
    background-color: rgba(255, 82, 82, 0.63);
  }

  .SmallButton {
    font-size: 0.9vw;
    width: 50%;
    background-color: rgba(255, 82, 82, 0.63);
  }

  /* Estilos para los ataques actuales */
  .current-attacks {
    border: 1px solid #ccc;
    padding: 10px;
  }

  .attacks span {
    display: block;
    margin-top: 10px;
    border-bottom: 1px solid #ccc;
    padding-bottom: 5px;
  }

  /* Estilos adicionales para la primera columna */
  .store-column {
    width: 90%;
  }

  .store-image {
    margin-left: auto;
    margin-right: auto;
    width: 100%;
    display: block;
    margin-top: 5%;
    cursor: pointer;
  }

  /* Estilos adicionales para la segunda columna */
  .arena-column {
    background-size: cover;
    position: relative;
  }

  .movement-block {
    margin-top: 20%;
  }

  /* Estilos adicionales para la tercera columna */
  .backpack-column {
    width: 90%;
  }

  .backpack-column .attack-item {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
  }

  .backpack-column .attack-item img {
    margin-left: 10px;
    width: 50px; /* Ajusta según sea necesario */
  }

  @media (max-width: 600px) {
    .SmallButton {
      font-size: 4vw; /* Tamaño de fuente más grande para pantallas pequeñas */
      width: 80%; /* Ancho más grande para pantallas pequeñas */
    }
  }

</style>