<template>
  <q-page padding>
    <q-form
      @submit="onSubmit"
      @reset="onReset"
      class="row q-col-gutter-sm"
    >
      <q-input
        outlined
        v-model="form.title"
        label="Título"
        hint="Informe o título do seu artigo"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
      />

      <q-input
        outlined
        v-model="form.author"
        label="Autor"
        hint="Informe o nome do autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => val && val.length > 0 || 'Campo obrigatório']"
      />

      <div class="col-lg-12 col-xs-12">
        <q-editor
          v-model="form.content"
          min-height="15rem"
        />
      </div>
      <div class="col-12 q-gutter-sm">
        <q-btn label="Salvar" color="primary" class="float-right" icon="save" type="submit" />
        <q-btn label="Cancelar" color="white" class="float-right" text-color="primary" :to="{ name: 'home' }" />
      </div>
    </q-form>
  </q-page>

</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/service/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const { post, getById, update } = postsService()

    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        const response = await getById(route.params.id)
        form.value = response
      }
    })

    const onSubmit = async () => {
      try {
        if (route.params.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }

        $q.notify({ message: 'Artigo criado com successo', icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        $q.notify({ message: 'Erro ao criar o artigo', icon: 'error', color: 'negative' })
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }

})
</script>
