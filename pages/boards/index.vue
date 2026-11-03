<script setup lang="ts">
interface Board {
  boardId: number
  title: string
  writer: string
  content: string
  viewCount: number
  createdAt: string
  updatedAt: string | null
  isDeleted: boolean
}

interface PageInfo {
  page: number
  size: number
  totalCount: number
  totalPages: number
  offset: number
  startPage: number
  endPage: number
  hasPrevious: boolean
  hasNext: boolean
}

interface BoardListResponse {
  boards: Board[]
  pageInfo: PageInfo
  keyword: string | null
}

const route = useRoute()
const router = useRouter()

const page = ref(Number(route.query.page ?? 1))
const keyword = ref(String(route.query.keyword ?? ''))

const { data, pending, error, refresh } = await useFetch<BoardListResponse>(
  'http://localhost:8080/api/boards',
  {
    query: {
      page,
      keyword
    },
    watch: false
  }
)

const search = async () => {
  page.value = 1

  await router.push({
    path: '/boards',
    query: {
      page: page.value,
      keyword: keyword.value || undefined
    }
  })

  await refresh()
}

const resetSearch = async () => {
  page.value = 1
  keyword.value = ''

  await router.push({
    path: '/boards',
    query: {
      page: 1
    }
  })

  await refresh()
}

const movePage = async (pageNum: number) => {
  page.value = pageNum

  await router.push({
    path: '/boards',
    query: {
      page: page.value,
      keyword: keyword.value || undefined
    }
  })

  await refresh()
}
</script>

<template>
  <div style="padding: 30px;">
    <h1>게시판</h1>

    <div style="margin-bottom: 16px;">
      <NuxtLink to="/">홈으로</NuxtLink>
      <NuxtLink to="/boards/new" style="margin-left: 8px;">
        글쓰기
      </NuxtLink>
    </div>

    <form @submit.prevent="search" style="margin-bottom: 16px;">
      <input
        v-model="keyword"
        type="text"
        placeholder="검색어를 입력하세요"
        style="width: 260px;"
      >

      <button type="submit" style="margin-left: 8px;">
        검색
      </button>

      <button type="button" style="margin-left: 8px;" @click="resetSearch">
        초기화
      </button>
    </form>

    <p v-if="pending">불러오는 중...</p>

    <p v-else-if="error" style="color: red;">
      게시글을 불러오지 못했습니다.
    </p>

    <div v-else-if="data">
      <p>
        전체 게시글 수: {{ data.pageInfo.totalCount }}
      </p>

      <table
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
            <td>
              <NuxtLink :to="`/boards/${board.boardId}`">
                {{ board.title }}
              </NuxtLink>
            </td>
            <td>{{ board.writer }}</td>
            <td>{{ board.createdAt }}</td>
            <td>{{ board.viewCount }}</td>
          </tr>

          <tr v-if="data.boards.length === 0">
            <td colspan="5" style="text-align: center;">
              게시글이 없습니다.
            </td>
          </tr>
        </tbody>
      </table>

      <div style="margin-top: 20px;">
        <button
          type="button"
          :disabled="!data.pageInfo.hasPrevious"
          @click="movePage(data.pageInfo.page - 1)"
        >
          이전
        </button>

        <button
          v-for="pageNum in data.pageInfo.endPage - data.pageInfo.startPage + 1"
          :key="pageNum"
          type="button"
          :style="{
            marginLeft: '4px',
            fontWeight: data.pageInfo.page === data.pageInfo.startPage + pageNum - 1 ? 'bold' : 'normal'
          }"
          @click="movePage(data.pageInfo.startPage + pageNum - 1)"
        >
          {{ data.pageInfo.startPage + pageNum - 1 }}
        </button>

        <button
          type="button"
          style="margin-left: 4px;"
          :disabled="!data.pageInfo.hasNext"
          @click="movePage(data.pageInfo.page + 1)"
        >
          다음
        </button>
      </div>
    </div>
  </div>
</template>