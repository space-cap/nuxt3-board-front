<script setup lang="ts">
const { data, pending, error } = await useFetch('http://localhost:8080/api/boards')
</script>

<template>
  <div style="padding: 30px;">
    <h1>게시판</h1>

    <NuxtLink to="/">
      홈으로
    </NuxtLink>

    <p v-if="pending">불러오는 중...</p>

    <p v-if="error">
      게시글을 불러오지 못했습니다.
    </p>

    <table
      v-if="data"
      border="1"
      style="width: 100%; border-collapse: collapse; margin-top: 20px;"
    >
      <thead>
        <tr>
          <th>번호</th>
          <th>제목</th>
          <th>작성자</th>
          <th>작성일</th>
          <th>조회수</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="board in data.boards" :key="board.boardId">
          <td>{{ board.boardId }}</td>
          <td>{{ board.title }}</td>
          <td>{{ board.writer }}</td>
          <td>{{ board.createdAt }}</td>
          <td>{{ board.viewCount }}</td>
        </tr>

        <tr v-if="data.boards.length === 0">
          <td colspan="5">게시글이 없습니다.</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>