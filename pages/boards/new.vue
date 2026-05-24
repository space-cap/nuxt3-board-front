<script setup lang="ts">
const router = useRouter()

const form = reactive({
  title: '',
  writer: '',
  content: ''
})

const isSubmitting = ref(false)
const errorMessage = ref('')

const saveBoard = async () => {
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

    await $fetch('http://localhost:8080/api/boards', {
      method: 'POST',
      body: {
        title: form.title,
        writer: form.writer,
        content: form.content
      }
    })

    await router.push('/boards')
  } catch (error) {
    errorMessage.value = '게시글 등록 중 오류가 발생했습니다.'
    console.error(error)
  } finally {
    isSubmitting.value = false
  }
}
</script>

<template>
  <div style="padding: 30px;">
    <h1>게시글 작성</h1>

    <form @submit.prevent="saveBoard">
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
        {{ isSubmitting ? '저장 중...' : '저장' }}
      </button>

      <NuxtLink to="/boards" style="margin-left: 8px;">
        취소
      </NuxtLink>
    </form>
  </div>
</template>