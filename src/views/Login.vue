<template>
  <form class="card auth-card" @submit.prevent="onSubmit">
    <div class="card-content">
      <span class="card-title">Домашняя бухгалтерия</span>
      <div class="input-field">
        <input
          id="email"
          type="text"
          v-model.trim="email"
          :class="{ invalid: ($v.email.$dirty && !$v.email.required)||($v.email.$dirty && !$v.email.email) }"
        >
        <label for="email">Email</label>
        <small class="helper-text invalid" v-if="$v.email.$dirty && !$v.email.required" >Введите Email</small>
        <small class="helper-text invalid" v-else-if="$v.email.$dirty && !$v.email.email" >Неправильный Email</small>
      </div>
      <div class="input-field">
        <input
          id="password"
          type="password"
          v-model="pass"
          :class="{ invalid: ($v.pass.$dirty && !$v.pass.required)||($v.pass.$dirty && !$v.pass.minLength) }"
        >
        <label for="password">Пароль</label>

        <small class="helper-text invalid" v-if="$v.pass.$dirty && !$v.pass.required" >Введите пароль</small>
        <small class="helper-text invalid" v-else-if="$v.pass.$dirty && !$v.pass.minLength" >Пароль должен иметь не менее {{$v.pass.$params.minLength.min}} символов. Сейчас он {{pass.length}}</small>
      </div>
    </div>
    <div class="card-action">
      <div>
        <button
          class="btn waves-effect waves-light auth-submit"
          type="submit"
        >
          Войти
          <i class="material-icons right">send</i>
        </button>
      </div>

      <p class="center">
        Нет аккаунта?
        <router-link to="/register">Зарегистрироваться</router-link>
      </p>
    </div>
  </form>
</template>

<script>
import { email, required, minLength } from 'vuelidate/lib/validators'
import messages from '@/utils/messages'
export default {
  metaInfo () {
    return {
      title: this.$title('Login')
    }
  },
  name: 'Login',
  data: () => ({
    email: '',
    pass: ''
  }),
  validations: {
    email: { required, email },
    pass: { required, minLength: minLength(6) }
  },
  mounted () {
    if (messages[this.$route.query.message]) {
      this.$message(messages[this.$route.query.message])
    }
  },
  methods: {
    async onSubmit () {
      if (this.$v.$invalid) {
        this.$v.$touch()
        return
      }

      const formData = {
        email: this.email,
        password: this.pass
      }

      try {
        await this.$store.dispatch('login', formData)
        this.$router.push('/')
      } catch (e) {}
    }
  }
}
</script>

<style scoped>

</style>
