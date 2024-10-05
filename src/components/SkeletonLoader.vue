<template>
  <div class="skeleton-loader">
    <div class="skeleton-loader__image"></div>
    <div class="skeleton-loader__text">
      <div class="skeleton-loader__line"></div>
      <div class="skeleton-loader__line short"></div>
    </div>
  </div>
</template>

<style scoped>
.skeleton-loader {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 280px;
  height: 400px;
  border-radius: 10px;
  background-color: #e0e0e0;
  margin: 1rem;
}

.skeleton-loader__image {
  width: 100%;
  height: 70%;
  border-radius: 10px 10px 0 0;
  background-color: #c0c0c0;
}

.skeleton-loader__text {
  padding: 10px;
  width: 100%;
}

.skeleton-loader__line {
  height: 10px;
  background-color: #c0c0c0;
  margin-bottom: 8px;
  border-radius: 5px;
}

.skeleton-loader__line.short {
  width: 70%;
}
</style>
