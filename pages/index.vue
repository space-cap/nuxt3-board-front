<template>
  <div style="padding: 30px;">
    <h1>Nuxt3 게시판 프로젝트</h1>

    <p>Spring Boot API와 연결하는 게시판입니다.</p>

    <NuxtLink to="/boards">
      게시판으로 이동
    </NuxtLink>

     <div style="margin: 16px 0;">
  <NuxtLink to="/boards/new">
    글쓰기
  </NuxtLink>
</div>

  </div>

 

</template>