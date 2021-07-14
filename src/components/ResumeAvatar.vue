<template>
  <div class="avatar">
    <img :src="avatarUrl" alt="Image_name">
  </div>
</template>

<script>
export default {
  props: {
    avatarUrl: {
      type: String,
    }
  }
}
</script>

<style>
.avatar {
  display: flex;
  justify-content: center;
}

.avatar img {
  width: 150px;
  height: auto;
  border-radius: 50%;
}
</style>