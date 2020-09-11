<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container class="grey lighten-3 grid">
        <v-row>
          <v-col><strong>Nome</strong></v-col>
          <v-col><strong>E-mail</strong></v-col>
          <v-col><strong>Ação</strong></v-col>
        </v-row>
        <br>
        <v-row v-for="(usuario, value) in usuarios" :key="value">
          <v-col>{{usuario.nome}}</v-col>
          <v-col>{{usuario.email}}</v-col>
          <v-col>
            <v-btn class="mx-2" fab dark x-small color="primary"
              @click="editUser(usuario, value)">
              <v-icon dark>mdi-pencil</v-icon>
            </v-btn>
            <v-btn class="mx-2" fab dark x-small color="red"
              @click="removeUser(value)">
              <v-icon dark>mdi-minus</v-icon>
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
      <v-btn class="centralizar" rounded color="primary"  
        @click="edicao ? alterarUsuario() : salvarUsuario()">Salvar usuário</v-btn>
      <v-form v-model="valid">
        <v-container>
          <v-row>
            <v-col
              cols="12"
              md="4"
            >
              <v-text-field
                v-model="usuario.nome"
                :rules="nameRules"
                :counter="60"
                label="Nome"
                required
              ></v-text-field>
            </v-col>
            <v-col
              cols="12"
              md="4"
            >
              <v-text-field
                v-model="usuario.email"
                :rules="emailRules"
                label="E-mail"
                required
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-form>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  computed: {
    
  },
  data() {
    return {
      key: '',
      usuario: {nome: '', email: ''},
      edicao: false,
      usuarios: [],
      valid: false,
      nameRules: [
        v => !!v || 'Nome obrigatório',
        v => v.length <= 60 || 'O nome deve conter até 60 caracteres',
      ],
      emailRules: [
        v => !!v || 'E-mail obrigatório',
        v => /.+@.+/.test(v) || 'E-mail inválido',
      ],
    }
  },

  methods: {
    getUsuarios() {
      this.$http.get('usuarios.json').then(resp => {
        this.usuarios = resp.data
      })
    },
    alterarUsuario() {
      if (this.usuario.nome == '' || this.usuario.email == '') {
        alert('Nome ou e-mail inválidos')
        return
      }
      this.edicao = false
      this.$http.put(`usuarios/${this.key}.json`, this.usuario)
        .then( resp => {
          if (resp.status == 200) {
            this.clearUser()
            this.getUsuarios()
          }
        })
        .catch(error => {
          alert(error)
        })
    },
    salvarUsuario() {
      if (this.usuario.nome == '' || this.usuario.email == '') {
        alert('Nome ou e-mail inválidos')
        return
      }
      const user = {
        nome: this.usuario.nome,
        email: this.usuario.email
      }
      this.$http.post('usuarios.json', user)
        .then( resp => {
          if (resp.status == 200) {
            this.clearUser()
            this.getUsuarios()
          }
        })
        .catch(error => {
          alert(error)
        })
    },
    clearUser() {
      this.usuario.nome = ''
      this.usuario.email = ''
    },
    removeUser(key) {
      this.$http.delete(`usuarios/${key}.json`)
        .then(res => {
          if (res.status == 200){
            this.getUsuarios()
          }
        })
        .catch(error => {
          alert(error)
        })
    },
    editUser(user, key) {
      this.edicao = true
      this.key = key
      this.usuario = user
    },
  },
  mounted() {
    this.clearUser()
    this.getUsuarios()
  }
};
</script>

<style scoped>
  .centralizar {
    left: 20px;
    top: 20px;
  }

  .grid {
    top: 50px;
  }
</style>