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
        <small class="helper-text invalid" v-if="$v.pass.$dirty  && !$v.pass.required">Введите пароль</small>
        <small class="helper-text invalid" v-else-if="$v.pass.$dirty  && !$v.pass.minLength">Пароль должен состоять из {{$v.pass.$params.minLength.min}}. Сейчас он имеет {{pass.length}} символов</small>
      </div>
      <div class="input-field">
        <input
          id="confirm-password"
          type="password"
          v-model="confirmation"
          :class="{ invalid: ($v.confirmation.$dirty && !$v.confirmation.required)||($v.confirmation.$dirty && !$v.confirmation.sameAsPassword) }"
        >
        <label for="confirm-password">Подтвердите Пароль</label>
        <small class="helper-text invalid" v-if="$v.confirmation.$dirty  && !$v.confirmation.required">Подтвердите пароль</small>
        <small class="helper-text invalid" v-else-if="$v.confirmation.$dirty && !$v.confirmation.sameAsPassword">Пароли не совпадают</small>
      </div>
      <div class="input-field">
        <input
          id="name"
          type="text"
          v-model.trim="name"
          :class="{ invalid: ($v.name.$dirty && !$v.name.required || $v.name.$dirty && !$v.name.minLength) }"
        >
        <label for="name">Имя</label>
        <small class="helper-text invalid" v-if="$v.name.$dirty && !$v.name.required">Введите Имя</small>
        <small class="helper-text invalid" v-else-if="$v.name.$dirty && !$v.name.minLength">Имя не может содержать меньше, чем 2 символа</small>
      </div>
      <p>
        <label>
          <input
            type="checkbox"
            v-model="agree"
            :class="{ invalid: ($v.name.$dirty && !$v.agree.checked) }"
          />
          <span>С правилами согласен</span>
        </label>
      </p>
      <small class="helper-text invalid" v-if="$v.name.$dirty && !$v.agree.checked">Вы должны согласиться с правилами</small>
    </div>
    <div class="card-action">
      <div>
        <button
          class="btn waves-effect waves-light auth-submit"
          type="submit"
        >
          Зарегистрироваться
          <i class="material-icons right">send</i>
        </button>
      </div>

      <p class="center">
        Уже есть аккаунт?
        <router-link to="/login">Войти!</router-link>
      </p>
    </div>
  </form>
</template>

<script>
import { email, minLength, required, sameAs } from 'vuelidate/lib/validators'
export default {
  metaInfo () {
    return {
      title: this.$title('Register')
    }
  },
  name: 'Register',
  data: () => ({
    email: '',
    pass: '',
    confirmation: '',
    name: '',
    agree: false
  }),
  validations: {
    email: { required, email },
    pass: { required, minLength: minLength(6) },
    confirmation: { required, sameAsPassword: sameAs('pass') },
    name: { required, minLength: minLength(2) },
    agree: { required, checked: v => v }
  },
  methods: {
    async onSubmit () {
      if (this.$v.$invalid) {
        this.$v.$touch()
        return
      }

      const formData = {
        email: this.email,
        password: this.pass,
        name: this.name
      }
      try {
        await this.$store.dispatch('register', formData)
        this.$router.push('/')
      } catch (e) {}
    }
  }
}
</script>

<style scoped>

</style>
