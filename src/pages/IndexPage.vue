<template>
  <q-page padding>
    <q-table
      title="Artigos"
      :rows="posts"
      :columns="columns"
      row-key="id"
    >
      <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space />
        <q-btn color="primary" label="Criar" :to="{ name: 'formPost' }" />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn icon="edit" color="primary" dense size="sm" @click="handleEditPost(props.row.id)"></q-btn>
          <q-btn icon="delete" color="negative" dense size="sm" @click="handleDeletePost(props.row.id)"></q-btn>
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from 'vue'
import postsService from 'src/service/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const $q = useQuasar()
    const posts = ref([])
    const { list, remove } = postsService()
    const router = useRouter()
    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'Título', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'Autor', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Ações', align: 'right' }
    ]

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        posts.value = await list()
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Confirmação',
          message: `Você deseja apagar o artigo de id ${id}?`,
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'apagado com successo', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao apagar o artigo', icon: 'error', color: 'negative' })
        console.error(error)
      }
    }

    const handleEditPost = async (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
