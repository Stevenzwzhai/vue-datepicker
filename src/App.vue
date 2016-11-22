<template>
    <div id="app">
        <div class="main">
            <input type="text" @click="show" v-model="date" readonly="readonly">
        </div>
        <div v-if="showDatePicker">
            <DatePicker @cancle="cancle" @sure="sure" :selecteddate="olddate"></DatePicker>
        </div>
    </div>
</template>

<script>
import DatePicker from './components/DatePicker.vue'
export default {
    name: 'app',
    data () {
        return {
            showDatePicker:false,
            date:"",
            olddate:null
        }
    },
    methods:{
        show () {
            this.showDatePicker = true;
        },
        cancle () {
            this.showDatePicker = false;
        },
        sure (date) {
            this.olddate = date;
            this.showDatePicker = false;
            this.date = this.formatDate (date);

        },
        formatDate (date) {
            return date.getFullYear()+"-"+parseInt(date.getMonth()+1)+"-"+date.getDate();
        }
    },
    components:{
        DatePicker
    }
}
</script>

<style>
*{
  padding:0;
  margin:0;
  list-style:none;
}
.main{
    margin-top:50px;
    text-align: center;
}
input{
    text-align: center;
    width:120px;
    height:30px;
    outline:none;
    border:2px solid #afe;
    font-size: 20px;
    border-radius:30px;
    color:#aed;
}

</style>
