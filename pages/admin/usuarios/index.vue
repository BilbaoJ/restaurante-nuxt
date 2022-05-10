<template>
  <b-container>
    <h2>USUARIOS</h2>
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
      <template #cell(opciones)="row">
        <b-button size="sm" @click="loadUser(row)" class="mr-2">
          Modificar usuario
        </b-button>

        <b-button size="sm" @click="deleteUser(row)" class="mr-2">
          Eliminar
        </b-button>
      </template>
    </b-table>

    <alerts :message="message" />
  </b-container>
</template>
<script>
import alerts from "../../../components/alerts.vue";
export default {
  layout:"admin",
  components: { alerts },
  // Información a utilizar
  data() {
    return {
      headers: ["correo", "nombre", "universidad", "opciones"],
      users: [],
      user: {},
      universidades: ["Universidad de Medellín", "Universidad de Antioquia"],
      editing: false,
      message: "",
      opcionesAxios: null,
    };
  },
  // Método antes de que cargue la página
  beforeMount() {
    this.loadHeader();
    this.loadUsers();
  },
  //Métodos a usar
  methods: {
    loadHeader() {
      let token = localStorage.getItem("token");
      this.opcionesAxios = {headers:{token}};
    },
    async loadUsers() {
      //Cargar usuarios de la base de datos
      //para llamar una dependencia inyectada $nombrePaquete
      const url = "http://localhost:3001/api/v1/usuarios";
      let { data } = await this.$axios.get(url, this.opcionesAxios);
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
      let { data } = await this.$axios.post(url, this.user, this.opcionesAxios);
      this.message = data.message;
      this.loadUsers();
    },
    async updateUser(event) {
      event.preventDefault();

      const url = `http://localhost:3001/api/v1/usuarios/${this.user._id}`;
      let { data } = await this.$axios.put(url, this.user, this.opcionesAxios);
      console.log(data);
      this.$swal.fire("Actualizado!", data.message, "success");
      //this.message = data.message; Para el componente de alerta personalizado
      this.resetForm();
      this.loadUsers();
    },
    loadUser(user) {
      this.user = Object.assign({}, user.item);
      this.editing = true;
    },
    async deleteUser({ item }) {
      this.$swal
        .fire({
          title: "Está seguro de eliminar?",
          text: "No se puede recuperar después de la eliminacoón",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Sí, eliminar!",
          cancelButtonText: "Cancelar",
        })
        .then(async (result) => {
          if (result.value) {
            const url = `http://localhost:3001/api/v1/usuarios/${item._id}`;
            let { data } = await this.$axios.delete(url, this.opcionesAxios);
            this.loadUsers();
            this.$swal.fire("Eliminado!", data.message, "success");
          }
        });
    },
    resetForm() {
      // Reset our form values
      this.editing = false;
      this.user = {};
    },
  },
};
</script>