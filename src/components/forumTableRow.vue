// рядок що відбражає 1 формум
<template>
    <tr>        
        <td> {{index}} </td>
        <td> {{forum.theme}} </td>
        <td> {{new Date(forum.date).toLocaleDateString()}} </td>
        <td> {{forum.shortDescribe}} </td>
        <td> {{forum.countOfSpeaks}} </td>
        <td>
            <input type="button" value="Вилучити" @click="remove(index)">
            <input type="button" value="Редагувати" @click="update(index)">
        </td>        
    </tr>
</template>

<script>
export default {
    name:"forumTableRow",
    props:{
        index:Number,
        forum:Object        
    },
    data(){   
        return{

        }
    },
    methods:{        
        remove(index){
            this.$emit("remove", index);
        },
  
        update(index){
            this.$emit("update",index);
        }
    }
}
</script>

<style scoped>
    td{
        border: 1px solid black;
    }
</style>