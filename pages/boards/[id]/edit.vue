<script setup lang="ts">
const route = useRoute()
const router = useRouter()

const boardId = route.params.id

const form = reactive({
  title: '',
  writer: '',
  content: ''
})

const isSubmitting = ref(false)
const errorMessage = ref('')

const { data: board, pending, error } = await useFetch(
  `http://localhost:8080/api/boards/${boardId}`
)

watchEffect(() => {
  if (board.value) {
    form.title = board.value.title
    form.writer = board.value.writer
    form.content = board.value.content
  }
})

const updateBoard = async () => {
  if (!form.title.trim()) {
    errorMessage.value = '제목을 입력하세요.'
    return
  }

  if (!form.writer.trim()) {
    errorMessage.value = '작성자를 입력하세요.'
    return
  }

  if (!form.content.trim()) {
    errorMessage.value = '내용을 입력하세요.'
    return
  }

  try {
    isSubmitting.value = true
    errorMessage.value = ''

    await $fetch(`http://localhost:8080/api/boards/${boardId}`, {
      method: 'PUT',
      body: {
        title: form.title,
        writer: form.writer,
        content: form.content
      }
    })

    await router.push(`/boards/${boardId}`)
  } catch (error) {
    errorMessage.value = '게시글 수정 중 오류가 발생했습니다.'
    console.error(error)
  } finally {
    isSubmitting.value = false
  }
}
</script>

<template>
  <div style="padding: 30px;">
    <h1>게시글 수정</h1>

    <p v-if="pending">불러오는 중...</p>

    <p v-if="error">
      게시글을 불러오지 못했습니다.
    </p>

    <form v-if="board" @submit.prevent="updateBoard">
      <div style="margin-bottom: 12px;">
        <label>제목</label><br>
        <input
          v-model="form.title"
          type="text"
          style="width: 400px;"
        >
      </div>

      <div style="margin-bottom: 12px;">
        <label>작성자</label><br>
        <input
          v-model="form.writer"
          type="text"
          style="width: 400px;"
        >
      </div>

      <div style="margin-bottom: 12px;">
        <label>내용</label><br>
        <textarea
          v-model="form.content"
          rows="10"
          style="width: 400px;"
        />
      </div>

      <p v-if="errorMessage" style="color: red;">
        {{ errorMessage }}
      </p>

      <button type="submit" :disabled="isSubmitting">
        {{ isSubmitting ? '수정 중...' : '수정 완료' }}
      </button>

      <NuxtLink :to="`/boards/${boardId}`" style="margin-left: 8px;">
        취소
      </NuxtLink>
    </form>
  </div>
</template>