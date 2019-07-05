<template>
  <v-container>
    <a
      class="back-button"
      href="#"
      @click.prevent="$router.push({ name: 'auth.signin' })"
    >
      <v-icon large>arrow_back</v-icon>
    </a>

    <v-card
      id="signup"
      flat
    >
      <v-card-title primary-title>
        <h3 class="headline mb-0">
          Cadastrar
        </h3>
      </v-card-title>

      <v-card-text>
        <v-layout
          row
          wrap
        >
          <v-flex xs12>
            <v-text-field
              v-model="user.name"
              name="name"
              label="Nome"
              color="secondary"
              required
              :error="error_name"
              :error-messages="error_message_name"
            />
          </v-flex>

          <v-flex xs12>
            <v-text-field
              v-model="user.email"
              type="email"
              name="email"
              label="E-mail"
              color="secondary"
              required
              :error="error_mail"
              :error-messages="error_messages_mail"
            />
          </v-flex>

          <v-flex xs12>
            <v-text-field
              v-model="user.document"
              :mask="'###.###.###-##'"
              type="text"
              name="document"
              label="CPF"
              color="secondary"
              required
              :error="error_document"
              :error-messages="error_messages_document"
            />
          </v-flex>

          <v-flex xs12>
            <v-text-field
              v-model="user.company"
              name="company"
              label="Empresa"
              color="secondary"
              required
              :error="error_company"
              :error-messages="error_messages_company"
            />
          </v-flex>

          <v-flex xs12>
            <v-text-field
              v-model="user.password"
              type="password"
              name="password"
              label="Senha"
              color="secondary"
              required
              :error="error_password"
              :error-messages="error_messages_password"
            />
          </v-flex>

          <v-flex xs12>
            <v-text-field
              v-model="confirmPassword"
              type="password"
              name="password"
              label="Confirme a senha"
              color="secondary"
              required
              :error="error_password_confirm"
              :error-messages="error_messages_password_confirm"
            />
          </v-flex>
        </v-layout>
      </v-card-text>

      <v-card-actions>
        <v-btn
          primary
          large
          block
          :loading="loading"
          color="secondary"
          @click="handleSubmit()"
        >
          Salvar
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
import sha1 from 'js-sha1'

export default {
  name: 'AuthSignUp',

  data: () => ({
    loading: false,
    confirmPassword: '',
    user: {
      name: null,
      email: null,
      password: null,
      company: null,
      document: null
    },
    error_name: false,
    error_messages_name: [],
    error_mail: false,
    error_messages_mail: [],
    error_document: false,
    error_messages_document: [],
    error_company: false,
    error_messages_company: [],
    error_password: false,
    error_messages_password: [],
    error_password_confirm: false,
    error_messages_password_confirm: []
  }),

  methods: {
    handleSubmit() {
      this.loading = true

      // this.$db.ref(`users/${this.user.document}`).push(this.user)

      this.$db.ref(`users/${sha1(this.user.email)}`)
        .set(this.user)
        .then(() => {
          alert('Usuário criado')
        })

      this.$db.ref(`companies/${this.user.company}`)
        .set({ name: this.user.company })
        .then(() => {
          alert('Empresa criada')
        })

      this.$auth.createUserWithEmailAndPassword(this.user.email, this.user.password)
        .then(() => {
          alert('Usuário criado com sucesso!')
          this.$auth.signOut()
        })
        .catch((error) => {
          const errorCode = error.code
          const errorMessage = error.message
          if (errorCode === 'auth/weak-password') {
            alert('The password is too weak.')
          } else {
            alert(errorMessage)
          }
          console.log(error)
        })
        .finally(() => {
          this.loading = false
        })
      return true
    },

    validateForm() {
      this.clearErrorMessages()
      let valueReturn = true

      if (this.name === '' || this.name === null) {
        this.error_name = true
        this.error_messages_name.push('Obrigatório')
        valueReturn = false
      }

      if (this.email === '' || this.email === null) {
        this.error_mail = true
        this.error_messages_mail.push('Obrigatório')
        valueReturn = false
      }

      if (this.document === '' || this.document === null) {
        this.error_document = true
        this.error_messages_document.push('Obrigatório')
        valueReturn = false
      }

      if (this.company === '' || this.company === null) {
        this.error_company = true
        this.error_messages_company.push('Obrigatório')
        valueReturn = false
      }

      if (this.password === '' || this.password === null) {
        this.error_password = true
        this.error_messages_password.push('Obrigatório')
      }

      if (this.confirmPassword === '' || this.confirmPassword === null) {
        this.error_password_confirm = true
        this.error_messages_password_confirm.push('Obrigatório')
      }

      return valueReturn
    }
  },

  clearErrorMessages() {
    this.error_name = false
    this.error_messages_name = []
    this.error_mail = false
    this.error_messages_mail = []
    this.error_document = false
    this.error_messages_document = []
    this.error_company = false
    this.error_messages_company = []
    this.error_password = false
    this.error_messages_password = []
    this.error_password_confirm = false
    this.error_messages_password_confirm = []
  }
}
</script>

<style>
.back-button {
  z-index: 9999;
  position: absolute;
  color: transparent !important;
}
.v-card.v-card--flat.v-sheet.theme--light {
  z-index: 999;
}
.theme--light.v-sheet#signup {
  background-color: transparent !important;
}
</style>
