<template>
  <div class="col s12 m6">
    <div>
      <div class="page-subtitle">
        <h4>Редактировать</h4>
      </div>

      <form @submit.prevent="onSubmit">
        <div class="input-field" >
          <select ref="select" v-model="current">
            <option v-for="cat of categories" :key="cat.id" :value="cat.id">{{ cat.title }}</option>
          </select>
          <label>Выберите категорию</label>
        </div>

        <div class="input-field">
          <input
            id="name"
            type="text"
            v-model="title" :class="{ invalid: $v.title.$dirty && !$v.title.required }"
          >
          <label for="name">Название</label>
          <span class="helper-text invalid" v-if="$v.title.$dirty && !$v.title.required" >Введите название</span>
        </div>

        <div class="input-field">
          <input
            id="limit"
            type="number"
            v-model.number="limit" :class="{ invalid: $v.limit.$dirty && !$v.limit.minValue }"
          >
          <label for="limit">Лимит</label>
          <span class="helper-text invalid" v-if="$v.limit.$dirty && !$v.limit.minValue">Минимальная величина {{$v.limit.$params.minValue.min}}</span>
        </div>

        <button class="btn waves-effect waves-light" type="submit">
          Обновить
          <i class="material-icons right">send</i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { required, minValue } from 'vuelidate/lib/validators'

export default {
  props: ['categories'],
  name: 'CategoryEdit',
  data: () => ({
    select: null,
    title: '',
    limit: 0,
    current: null
  }),
  validations: {
    title: { required },
    limit: { minValue: minValue(1) }
  },
  mounted () {
    M.updateTextFields()
    M.FormSelect.init(this.$refs.select)
  },
  watch: {
    current(catId){
      const { title, limit } = this.categories.find(c => c.id === catId)
      this.title = title
      this.limit = limit
    }
  },
  created() {
    const {id, title, limit} = this.categories[0]
    this.current = id
    this.title = title
    this.limit = limit
  },
  destroyed() {
    if (this.select && this.select.destroy){
      this.select.destroy
    }
  },
  methods: {
    async onSubmit () {
      if (this.$v.$invalid) {
        this.$v.$touch()
        return
      }

      try {
        const categoryData = {
          id: this.current,
          title: this.title,
          limit:this.limit
        }
        const category = await this.$store.dispatch('updateCategory', categoryData)
        this.$message(`Категория успешно обновлена`)
        this.$emit('updated', categoryData)
      } catch (e) {}
    }
  }
}
</script>

<style scoped>

</style>
