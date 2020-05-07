<template>
  <div id="app">    
    <messageSystem v-bind:messagesList="messagesList"></messageSystem>
    <searchString v-model="searchText"></searchString>
    <queryForm v-model="queryObject"></queryForm>
    <input type="button" @click="showNewForumForm()" value=" Додати кнгу" />
    <forumTable v-bind:forumList="filtredForums" @remove="removeForum" @update="showUpdateForumForm"></forumTable>
    <forumForm v-model="forum" v-bind:visible="formVisible" @input="formInput"></forumForm>
  </div>
</template>

<script>
import Vue from "vue";
import forumTable from "./components/forumTable";
import forumForm from "./components/forumForm";
import searchString from "./components/searchString";
import queryForm from "./components/queryForm";
import messageSystem from "./components/messageSystem";
import axios from "axios";

export default {
  //назва компоненту, співпадає із назвою файла  
  name: "app",
  //компоненти які використовуються в додатку
  components: {
    forumTable,
    forumForm,
    searchString,
    queryForm,
    messageSystem
  },
  //дані компонента на початку
  data() {
    return {
      //чписок всіх формумів
      forums: [],
      //обєкт 1 порожнього формума
      forum: {
        theme: "",
        shortDescribe: "",
        countOfSpeaks:0,
        date: "1997-01-10T22:00:00.000Z",
      },
      //список повідомлень
      messagesList: [],
      //видимість форми
      formVisible: false,
      //індекс вибраного формума
      selectedIndex: -1,
      //текст пошуку
      searchText: "",
      //об'єкт з параметрами пошуку 
      queryObject: {
        countOfSpeaks: null
      }
    };
  },
  // значення які необхідно обчислити.  
  computed: {
    //відфільтрований список формумів
    filtredForums() {
      let result = this.forums;
      if (this.searchText)
        result = result.filter(forum =>
          forum.theme.toLowerCase().includes(this.searchText.toLowerCase())
        );
      console.log(result);
      if (this.queryObject.shortDescribe)
        result = result.filter(forum => forum.pages >= this.queryObject.shortDescribe);
      console.log(result);
      if (this.queryObject.date)
        result = result.filter(forum =>forum.date <= this.queryObject.date);
      if (this.queryObject.countOfSpeaks)
        result = result.filter(forum => forum.countOfSpeaks <= this.queryObject.countOfSpeaks);
      return result;
    }
  },
  //коли додаток створено то треба завантажити список формумів із сервера 
  mounted() {
    this.getforums().then(forums => {
      this.forums = forums;
    });
  },
  //методи 
  methods: {
    // асинхронний хук по завантаження всіх формумів
    async getForums() {
      try {
        //чекаємо відповідь на запит GET  http://localhost:5000/forum
        let resp = await axios.get("http://localhost:5000/forum");
        //якщо все добре то повертаємо данні із відповіді на запит 
        return resp.data;
      } catch (e) {
        //якщо сталась помилка (в тому числі і сервер повернув 500)
        console.log(e);
        //додати повідомлення 
        this.messagesList.push(e);
      }
    },
    // асинхронний хук по додаванню 1 формума
    async postForum(forum) {
      try {
        let resp = await axios.post("http://localhost:5000/forum", forum);
        return resp.data;
      } catch (e) {
        console.log(e);
        this.messagesList.push(e);
      }
    },
    // асинхронний хук по вилученню 1 формума
    async deleteFrum(forum) {
      try {
        let resp = await axios.delete(`http://localhost:5000/forum/${forum._id}`);
        return resp.data;
      } catch (e) {
        console.log(e);
        this.messagesList.push(e);
      }
    },
    // асинхронний хук по оновленню інформації про формум
    async patchForum(forum) {
      try {
        let resp = await axios.patch(
          `http://localhost:5000/forum/${forum._id}`,
          forum
        );
        return resp.data;
      } catch (e) {
        console.log(e);
        this.messagesList.push(e);
      }
    },
    // показуємо форму для нового формума  
    showNewForumForm() {
      this.forum = Object.assign(this.forum, {
        theme: "",
        shortDescribe: "",
        countOfSpeaks: 0,
        date: "1997-01-10T22:00:00.000Z",
      });
      this.formAction = this.addNewForum;
      this.formVisible = true;
    },
    //показуємо форму для оновлення формума
    showUpdateForumForm(index) {
      this.selectedIndex = index;
      Object.assign(this.forum, this.filtredForums[index]);
      this.formAction = this.updateForum;
      this.formVisible = true;
    },
    //додавання нового формума
    addNewForum() {
      //спочатку робимо хук 
      this.postForum(this.forum).
      //потім додаємо формум в список якщо хук пройшов вдало
      then(forum => this.forums.push(forum));
    },
    //вилучення формума
    removeForum(index) {
      this.deleteForum(this.filtredForums[index]).
      then(() => {
        this.filtredForums.splice(index, 1);
      });
    },
    //оновити формум
    updateForum() {
      let i = this.selectedIndex;
      this.patchForum(this.forum).then(forum => {
        Object.assign(this.filtredForums[i], forum);
      });
      this.selectedIndex = -1;
    },
    //показати форму редагування або оновлення формума
    formInput() {
      this.formAction();
      this.formVisible = false;
    },
    formAction: function() {}
  }
};
</script>

<style scoped>
#app {
  margin: 0;
  padding: 0;
}
</style>