<template>
  <b-container>
    <h2>USUARIO</h2>
    <!-- Creación de formulario -->
    <b-form @submit="createUser" @reset="resetForm">
      <b-form-group
        id="input-group-1"
        label="Email:"
        label-for="input-1"
        description="We'll never share your email with anyone else."
      >
        <b-form-input
          id="input-1"
          v-model="user.correo"
          type="email"
          placeholder="Enter email"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-2" label="Your Name:" label-for="input-2">
        <b-form-input
          id="input-2"
          v-model="user.nombre"
          placeholder="Enter name"
          required
        ></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-3" label="University:" label-for="input-3">
        <b-form-select
          id="input-3"
          v-model="user.universidad"
          :options="universidades"
          required
        ></b-form-select>
      </b-form-group>
      <br />
      <!-- Actions -->
      <b-button type="submit" variant="primary" v-if="!editing"
        >Crear Usuario</b-button
      >
      <b-button variant="success" @click="updateUser" v-else
        >Guardar Usuario</b-button
      >
      <b-button type="reset" variant="danger">Limpiar Formulario</b-button>
    </b-form>

    <b-table striped hover :items="users" :fields="headers">
      <template #cell(show_details)="row">
        <b-button size="sm" @click="loadUser(row)" class="mr-2">
          Modificar usuario
        </b-button>

        <b-button size="sm" @click="deleteUser(row)" class="mr-2">
          Eliminar
        </b-button>
      </template>
    </b-table>
  </b-container>
</template>
<script>
export default {
    // Información a utilizar
  data() {
    return {
      headers: ["correo", "nombre", "universidad", "show_details"],
      users: [],
      user: {},
      universidades: ["Universidad de Medellín", "Universidad de Antioquia"],
      editing: false,
    };
  },
  // Método antes de que cargue la página
  beforeMount() {
    this.loadUsers();
  },
  //Métodos a usar
  methods: {
    async loadUsers() {
      //Cargar usuarios de la base de datos
      //para llamar una dependencia inyectada $nombrePaquete
      const url = "http://localhost:3001/api/v1/usuarios";
      let { data } = await this.$axios.get(url);
      console.log(data);
      if (data.ok) {
        this.users = data.info;
      } else {
        alert("No se cargaron los usuarios");
      }
    },
    async createUser(event) {
      event.preventDefault();

      const url = "http://localhost:3001/api/v1/usuarios";
      let { data } = await this.$axios.post(url, this.user);
      console.log(data);
      this.loadUsers();
    },
    async updateUser(event) {
      event.preventDefault();

      const url = `http://localhost:3001/api/v1/usuarios/${this.user._id}`;
      let { data } = await this.$axios.put(url, this.user);
      console.log(data);
      this.resetForm();
      this.loadUsers();
    },
    loadUser(user) {
      this.user = Object.assign({}, user.item);
      this.editing = true;
    },
    async deleteUser({ item }) {
      const url = `http://localhost:3001/api/v1/usuarios/${this.item._id}`;
      let { data } = await this.$axios.delete(url);
      this.loadUsers();
    },
    resetForm() {
      // Reset our form values
      this.editing = false;
      this.user = {};
    },
  },
};
</script>