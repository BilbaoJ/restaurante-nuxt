<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-navbar-brand to="/admin/dashboard"
        >Restaurante DLLO Web</b-navbar-brand
      >

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="/admin/usuarios">Usuarios</b-nav-item>
          <b-nav-item href="/admin/platos" disabled>Platos</b-nav-item>
          <b-nav-item href="/admin/pedidos" disabled>Pedidos</b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown right>
            <!-- Using 'button-content' slot -->
            <template #button-content> Hola {{ nombre }} </template>
            <b-dropdown-item href="#">Mi perfil</b-dropdown-item>
            <b-dropdown-item @click="signout">Salir</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <br />
    <Nuxt />
  </div>
</template>
<script>
export default {
  beforeMount() {
    this.loadUser();
  },
  data() {
    return {
      nombre: "",
    };
  },
  methods: {
    async loadUser() {
      try {
        this.nombre = localStorage.getItem("nombre");
        let url = "http://localhost:3001/api/v1/validate";
        let token = localStorage.getItem("token");
        let opcionesAxios = { headers: { token } };
        let { data } = await this.$axios.get(url, opcionesAxios);
        //Token correcto
        if (!data.ok) {
          this.$router.push("/");
        }
        if (data.info.rol != "ADMIN") {
          this.$router.push("/");
          await this.$swal.fire("Error!", "No está autorizado", "error");
        }
      } catch {
        this.$router.push("/");
      }
    },
    signout() {
      localStorage.clear();
      this.$router.push("/");
    },
  },
};
</script>