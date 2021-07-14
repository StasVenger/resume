<template>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="addNewContent">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="type">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>
      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" v-model="content" rows="3"></textarea>
      </div>
      <button class="btn primary" type="submit" :disabled="isDisabled">Добавить</button>
    </form>

    <div class="card card-w70">в
      <template v-if="resumeData.length !== 0">
        <div v-for="item in resumeData" :key="item.id">
          <resume-title :title="item.content" v-if="item.type === 'title'"></resume-title>
          <resume-avatar :avatarUrl="item.content" v-if="item.type === 'avatar'"></resume-avatar>
          <resume-sub-title :sub-title="item.content" v-if="item.type === 'subtitle'"></resume-sub-title>
          <resume-text :text="item.content" v-if="item.type === 'text'"></resume-text>
        </div>
      </template>
      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <div class="container">
    <p v-if="comments.length === 0">
      <button class="btn primary" @click="loadComments">Загрузить комментарии</button>
    </p>
    <comments :comments="comments" v-else></comments>
    <loader v-if="loading"></loader>
  </div>
</template>

<script>
import ResumeTitle from "@/components/ResumeTitle";
import ResumeAvatar from "@/components/ResumeAvatar";
import ResumeSubTitle from "@/components/ResumeSubTitle";
import ResumeText from "@/components/ResumeText";
import Loader from "@/components/Loader";
import Comments from "@/components/Comments";
import axios from "axios";

export default {
  data() {
    return {
      type: 'title',
      content: '',
      resumeData: [],
      comments: [],
      loading: false
    }
  },
  mounted() {
    this.loadContent();
  },
  methods: {
    async addNewContent() {
      let data = JSON.stringify({
        type: this.type,
        content: this.content
      })
      await axios.post('https://resume-d80ae-default-rtdb.firebaseio.com/resume.json', data);

      this.type = 'title';
      this.content = '';

      await this.loadContent();
    },
    async loadContent() {
      try {
        const {data} = await axios.get('https://resume-d80ae-default-rtdb.firebaseio.com/resume.json');
        if (!data) {
          throw new Error('Список пустой');
        }
        this.resumeData = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
      } catch (e) {
        console.error(e);
      }
    },
    async loadComments() {
      try {
        this.loading = true;
        const comments = await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42');
        this.comments = comments.data;
        this.loading = false;
      } catch (e) {
        console.error(e);
      }
    }
  },
  computed: {
    isDisabled() {
      return this.content.length <= 3
    }
  },
  components: {Comments, Loader, ResumeTitle, ResumeAvatar, ResumeSubTitle, ResumeText}
}
</script>