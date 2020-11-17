<template>
  <v-card>
    <v-toolbar color="deep-purple" dark>
      <v-toolbar-title class="text-center">Registrar Pedido</v-toolbar-title>
      <v-progress-linear
        :active="isLoading"
        :indeterminate="isLoading"
        absolute
        bottom
        color="yellow darken-2"
      ></v-progress-linear>
    </v-toolbar>
    <v-card-text>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-container class="form__register">
          <v-select
            v-model="selectProducto"
            :items="listaProductos"
            item-text="nombre"
            item-value="id"
            label="Producto"
            :rules="productoRules"
            return-object
            single-line
            required
          ></v-select>
          <v-text-field
            type="number"
            step="1"
            min="1"
            max="5"
            :rules="cantidadRules"
            v-model="cantidad"
            label="Cantidad"
            clearable
            required
          ></v-text-field>
          <v-textarea
            v-model="direccion"
            label="Dirección de Envio"
            :rules="direccionRules"
            required
            :counter="50"
            clearable
            clear-icon="mdi-close-circle"
          ></v-textarea>
          <v-select
            v-model="selectZona"
            :items="listaZonas"
            item-text="nombre"
            item-value="id"
            label="Zona"
            :rules="zonaRules"
            return-object
            single-line
            required
          ></v-select>
          <v-row>
            <v-col cols="12" md="6">
              <v-text-field
                v-model="nombre"
                :counter="50"
                :rules="nombreRules"
                label="Nombre"
                clearable
                required
              ></v-text-field>
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="apellido"
                :counter="50"
                :rules="apellidoRules"
                label="Apellido"
                clearable
                required
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12" md="6">
              <v-text-field
                type="number"
                step="1"
                min="1"
                max="9999999"
                maxlength="7"
                v-model="ci"
                label="N° Carnet Identidad"
                :rules="ciRules"
                clearable
                required
              ></v-text-field>
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                type="number"
                step="1"
                min="1"
                max="99999999"
                maxlength="8"
                v-model="telefono"
                label="N° Telefono"
                :rules="telefonoRules"
                clearable
                required
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-form>
      <v-snackbar v-model="snackbarSuccess" color="success" shaped>
        {{ snackbarSuccessMessage }}
        <template v-slot:action="{ attrs }">
          <v-btn
            color="white"
            text
            v-bind="attrs"
            @click="snackbarSuccess = false"
          >
            Cerrar
          </v-btn>
        </template>
      </v-snackbar>
      <v-snackbar v-model="snackbarFailure" color="danger" shaped>
        {{ snackbarFailureMessage }}
        <template v-slot:action="{ attrs }">
          <v-btn
            color="white"
            text
            v-bind="attrs"
            @click="snackbarFailure = false"
          >
            Cerrar
          </v-btn>
        </template>
      </v-snackbar>
    </v-card-text>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        :disabled="!valid"
        color="success"
        class="mr-4"
        @click="registerPedido"
      >
        Registrar
      </v-btn>

      <v-btn
        color="error"
        class="mr-4"
        @click="limpiarFormularioRegistrarPedido"
      >
        Limpiar
      </v-btn>
      <v-spacer></v-spacer>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: "PedidoRegisterFormCard",
  data() {
    return {
      valid: false,
      isLoading: false,
      isSuccess: false,
      showMessages: false,
      // Snackbar
      snackbarSuccess: false,
      snackbarFailure: false,
      snackbarSuccessMessage: "¡Registro exitoso!",
      snackbarFailureMessage:
        "Hubo un problema con el procesamiento de la información. Por favor!, vuelva intentarlo.",
      // Validation Rules
      cantidadRules: [
        (v) => !!v || "Cantidad es requerida",
        (v) => !isNaN(parseInt(v)) || "Cantidad no es un numero",
        (v) =>
          (v >= 1 && v <= 5) ||
          "El valor de cantidad debe ser dentro del rango del 1 a 5",
      ],
      nombreRules: [
        (v) => !!v || "Nombre es requerido",
        (v) =>
          (/^[a-zA-ZñÑáéíóúÁÉÍÓÚ\s]+$/i.test(v)) || "Caracters no validos",
        (v) => (v.trim().length >= 3) || "El nombre debe tener al menos de 3 caracters",
      ],
      apellidoRules: [
        (v) => !!v || "Apellido es requerido",
        (v) =>
          (/^[a-zA-ZñÑáéíóúÁÉÍÓÚ\s]+$/i.test(v)) || "Caracters no validos",
        (v) => (v.trim().length >= 3) || "El apellido debe tener al menos de 3 caracters",
      ],
      zonaRules: [
        (v) => !!v.id || "Zona es requerida",
      ],
      direccionRules: [
        (v) => !!v || "Dirección envio es requerida",
        (v) => (v.trim().length > 0) || "Dirección envio es requerida",
      ],
      ciRules: [
        (v) => !!v || "Carnet de identidad es requerida",
        (v) =>
          (v.length >= 7 && v.length <= 10) ||
          "El carnet de identidad debe tener al menos de 7 dígitos",
      ],
      telefonoRules: [
        (v) => !!v || "Teléfono es requerido",
        (v) =>
          (v.length >= 7 && v.length <= 8) ||
          "El teléfono debe tener entre 6 a 8 dígitos",
      ],
      productoRules: [
        (v) => !!v.id || "Producto es requerido",
      ],
      // Pedido
      nombre: "",
      apellido: "",
      ci: 0,
      direccion: "",
      zona_id: 0,
      telefono: 0,
      producto_id: 0,
      cantidad: 0,
      // Lista de datos
      listaZonas: [],
      listaProductos: [],
      // Elementos seleccionados de las listas
      selectZona: { id: 0, nombre: "" },
      selectProducto: { id: 0, nombre: "" },
      // Nuestro Backend: API ENDPOINT
      // SERVER_API_BACKEND: `https://my-json-server.typicode.com/aledevbol123/backend-embotelladora`,
      SERVER_API_BASE_API: `http://159.203.189.218:3000/api/v1`,
    };
  },
  mounted() {
    this.callToApiObtenerZonas();
    this.callToApiObtenerProductos();
  },
  methods: {
    registerPedido() {
      if(this.$refs.form.validate()){
        this.isLoading = true;

        // valor de cantidad tiene que ser mayor que cero y no puede exceder 5.
        const condicionBotellones = this.cantidad > 0 && this.cantidad <= 5;

        if (condicionBotellones) {
          const data = this.convertirPedidoValues();
          console.log("Data->", data);
          // Llamando al servicio "Registra Pedido"
          this.callToApiRegisterPedido(data);
        } else {
          this.isLoading = false;
          this.snackbarFailure = true;
          this.snackbarFailureMessage = "El valor de cantidad debe ser de 1 a 5";
        }

        this.isLoading = false;
        this.showMessages = true;
        this.limpiarFormularioRegistrarPedido();
      }
    },
    limpiarFormularioRegistrarPedido() {
      this.nombre = "";
      this.apellido = "";
      this.ci = 0;
      this.direccion = "";
      this.zona_id = 0;
      this.telefono = 0;
      this.producto_id = 0;
      this.cantidad = 0;
      // Elementos seleccionados de las listas
      this.selectZona = { id: 1, nombre: "" },
      this.selectProducto = { id: 1, nombre: "" },
      // Ocultar los mensajes
      this.showMessages = false;
    },
    validate() {
      this.$refs.form.validate();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
    convertirPedidoValues() {
      return {
        nombre: this.nombre,
        apellido: this.apellido,
        ci: this.ci === null || this.ci === undefined ? 0 : parseInt(this.ci),
        direccion: this.direccion,
        zona_id:
          this.selectZona.id === null || this.selectZona.id === undefined
            ? 0
            : parseInt(this.selectZona.id),
        telefono:
          this.telefono === null || this.telefono === undefined
            ? 0
            : parseInt(this.telefono),
        producto_id:
          this.selectProducto.id === null ||
          this.selectProducto.id === undefined
            ? 0
            : parseInt(this.selectProducto.id),
        cantidad:
          this.cantidad === null || this.cantidad === undefined
            ? 0
            : parseInt(this.cantidad),
      };
    },
    callToApiObtenerZonas() {
      // URL del API REST BACKEND "zona"
      const API_RESOURCE = `zona`;
      const URL_API_ZONAS = `${this.SERVER_API_BASE_API}/${API_RESOURCE}`;
      // Fetch data
      fetch(URL_API_ZONAS, {
        method: "GET",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
        },
      })
        .then((response) => response.json())
        .then((data) => {
          console.log("data zonas->", data);
          const { rows } = data;
          this.listaZonas = rows;
        })
        .catch((error) => {
          console.error(error);
          this.isLoading = false;
          this.snackbarFailure = true;
          this.snackbarFailureMessage =
            "Hubo un problema con la busqueda de las zonas";
        });
    },
    callToApiObtenerProductos() {
      // URL del API REST BACKEND "producto"
      const API_RESOURCE = `producto`;
      const URL_API_PRODUCTOS = `${this.SERVER_API_BASE_API}/${API_RESOURCE}`;
      // Fetch data
      fetch(URL_API_PRODUCTOS, {
        method: "GET",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
        },
      })
        .then((response) => response.json())
        .then((data) => {
          console.log("data productos->", data);
          const { rows } = data;
          this.listaProductos = rows;
        })
        .catch((error) => {
          console.error(error);
          this.isLoading = false;
          this.snackbarFailure = true;
          this.snackbarFailureMessage =
            "Hubo un problema con la busqueda de los productos";
        });
    },
    callToApiRegisterPedido(data) {
      // URL del API REST BACKEND "pedido/registrar"
      const API_RESOURCE = `pedido/registrar`;
      const URL_API_PEDIDOS_REGISTER = `${this.SERVER_API_BASE_API}/${API_RESOURCE}`;
      // Fetch data
      fetch(URL_API_PEDIDOS_REGISTER, {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(data),
      })
        .then((response) => response.json())
        .then((data) => {
          if (data !== null && data !== undefined) {
            console.log(data);
            this.snackbarSuccessMessage = "¡Registro exitoso!";
            this.isSuccess = true;
            this.snackbarSuccess = true;
            this.resetValidation();
          }
          this.isLoading = false;
        })
        .catch((error) => {
          console.error(error);
          this.isLoading = false;
          this.snackbarFailure = true;
          this.snackbarFailureMessage =
            "Hubo un problema con el procesamiento de la informacion. Por favor!, vuelva intentarlo.";
        });
    },
  },
};
</script>