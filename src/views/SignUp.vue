<template>
  <div>
    <Header />
    <v-main>
      <v-row justify="center">
        <v-col cols="12" sm="10" md="8" lg="6">
          <v-card ref="form">
            <v-toolbar color="black" dark flat>
              <v-toolbar-title> Cuentanos un poco de tí </v-toolbar-title>
            </v-toolbar>
            <v-card-text>
              <v-text-field
                ref="name"
                v-model="name"
                :rules="[() => !!name || 'This field is required']"
                :error-messages="errorMessages"
                label="Nombre"
                prepend-inner-icon="mdi-account-circle"
                placeholder="Ingrese su nombre"
                required
              ></v-text-field>
              <v-text-field
                ref="Apellido"
                v-model="lastname"
                :rules="[() => !!lastname || 'This field is required']"
                label="Apellido"
                prepend-inner-icon="mdi-account"
                placeholder="Ingrese su apellido"
                required
              ></v-text-field>
              <v-text-field
                ref="nickname"
                v-model="nickname"
                :rules="[() => !!nickname || 'This field is required']"
                label="nickname"
                prepend-inner-icon="mdi-account"
                placeholder="Ingrese su nickname"
                required
              ></v-text-field>
              <v-text-field
                v-model="email"
                :rules="emailRules"
                prepend-inner-icon="mdi-email"
                label="E-mail"
                required
              ></v-text-field>
              <v-text-field
                v-model="password"
                :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                :rules="[() => !!password || 'This field is required']"
                :type="show ? 'text' : 'password'"
                name="input-10-1"
                prepend-inner-icon="mdi-lock"
                label="Contraseña"
                @click:append="show = !show"
              ></v-text-field>
            </v-card-text>
            <v-card-actions>
              <v-btn color="red" text>Cancel</v-btn>
              <v-spacer></v-spacer>

              <v-btn color="black" text @click="submit">Registrarme</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-main>
  </div>
</template>

<script>
import Header from "@/components/Header";
import Footer from "@/components/Footer";
import swal from "sweetalert2";

export default {
  components: {
    Header,
    Footer,
  },
  data: () => {
    return {
      errorMessages: "",
      name: null,
      show: null,
      lastname: null,
      nickname: null,
      email: null,
      password: null,
      formHasErrors: false,
      emailRules: [
        (v) => !!v || "E-mail is required",
        (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
      ],
    };
  },
  methods: {
    async submit() {
      const response = await fetch("http://localhost:4000/api/user/", {
        method: "post",
        body: JSON.stringify({
          name: this.name,
          lastname: this.lastname,
          nickname: this.nickname,
          email: this.email,
          password: this.password,
        }),
        headers: {
          "Content-Type": "application/json",
        },
      });

      if (response.ok) {
        new swal("Listo!", "Se ha registrado correctamente", "success").then(
          () => {
            window.location.href = "/Login";
          }
        );
      } else {
        new swal("Error", "Revisa tu conexion a internet", "error");
      }
    },
  },
};
</script>

<style>
</style>