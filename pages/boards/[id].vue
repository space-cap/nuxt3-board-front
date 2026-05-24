<script setup lang="ts">
const route = useRoute()

const boardId = route.params.id

const { data: board, pending, error } = await useFetch(
  `http://localhost:8080/api/boards/${boardId}`
)
</script>

<template>
  <div style="padding: 30px;">
    <h1>게시글 상세</h1>

    <p v-if="pending">불러오는 중...</p>

    <p v-if="error">
      게시글을 불러오지 못했습니다.
    </p>

    <div v-if="board">
      <table border="1" style="width: 100%; border-collapse: collapse;">
        <tbody>
          <tr>
            <th style="width: 120px;">번호</th>
            <td>{{ board.boardId }}</td>
          </tr>

          <tr>
            <th>제목</th>
            <td>{{ board.title }}</td>
          </tr>

          <tr>
            <th>작성자</th>
            <td>{{ board.writer }}</td>
          </tr>

          <tr>
            <th>작성일</th>
            <td>{{ board.createdAt }}</td>
          </tr>

          <tr>
            <th>조회수</th>
            <td>{{ board.viewCount }}</td>
          </tr>

          <tr>
            <th>내용</th>
            <td style="white-space: pre-line;">
              {{ board.content }}
            </td>
          </tr>
        </tbody>
      </table>

     <div style="margin-top: 20px;">
  <NuxtLink to="/boards">목록으로</NuxtLink>

  <NuxtLink
    :to="`/boards/${board.boardId}/edit`"
    style="margin-left: 8px;"
  >
    수정
  </NuxtLink>
</div>
    </div>
  </div>
</template>