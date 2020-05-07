// таблична форма відображення формумів
<template>
    <div>
        <p v-if="forumList.length==0" class="alert">
            форуми відсутні
        </p>
        
        <table v-if="forumList.length>0">
            <tr>
                <th>#</th>
                <th v-on:click="sort('theme')">  Тема </th>
                <th v-on:click="sort('short describe')">  Короткий опис </th>
                <th v-on:click="sort('date published')"> Дата публікації </th>
                <th v-on:click="sort('count of speaks')"> Кількість обговорень </th>
                <th></th>
            </tr>
            <forumTableRow v-for="(forum,index) in forumList" 
                v-bind:key="forum._id" 
                v-bind="{forum,index}"
                @remove="remove"
                @update="update" 
            >             
            </forumTableRow>
        </table>
    </div> 
</template>

<script>
import forumTableRow from "./forummTableRow";

export default {
    name:"forumTable",
    props:{
        List:Array,
    },
    data(){
        return{
           forums: this.forumList
        }
    },
    components:{
        forumTableRow
    },
    methods:{
        //сортування по деякому полю
        sort(field){
           this.forumList.sort((b1,b2)=> b1[field]>=b2[field]?1:-1);
        },
        //вилучити форум 
        remove(index){
            //генруємо подію, вилучення здійснить батьківський компонент
            this.$emit("remove",index);
        },  
        //оновити форум
        update(index){
            //генруємо подію, оеовлення здійснить батьківський компонент
            this.$emit("update",index);
        }
    }
}
</script>

<style scoped>
    .alert{
        background: yellow;
        color: crimson;
    }

    table, table td{
        border-collapse: collapse;
        border: 1px solid black;
    }
</style>