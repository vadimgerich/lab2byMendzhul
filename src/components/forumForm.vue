// Форма для відображення формума
<template>
    <form v-if="visible" @submit.prevent> <!-- якщо форма видима то показати її і відмінити надсилання запиту за замовчуванням-->
        <label> Тема форуму <input type="text" v-model="forum.theme" required> </label> <br>
        <label> Короткий опис <input type="text" v-model="forum.shortDescription" required></label> <br>
        <label> Дата обговорень <input type="date" v-model="forum.date"> </label> <br>
        <label> Кількість обговорень <input type="number" v-model.number="forum.countOfSpeaks" min="0"> </label> <br>
        <input type="button" @click="save()" value="Зберегти">     
    </form>
</template>

<script>
import setInput from "./setInput";

export default {
    name: "forumForm",
    props:{
        //передача значення в props через value і генерування події input необхідне для коректної роботи v-model компонента
        value: Object,
        visible: Boolean
    },
    data(){
        return{
            forum:this.value,            
        }
    },
    components:{
        setInput
    },
    methods:{
        save(){
            this.$emit('input', this.forum);         
        }
    }
}
</script>
<style scoped>
    form{
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        background: white ;
    }
</style>