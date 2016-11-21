<template>
    <div id="date-picker">
        <div class="header">
            <div @click="cancle">取消</div>
            <div @click="sure">确定</div>
        </div>
        <div class="content">
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(30,$event,'year')">
                    <li v-for="i in 30">{{1990+i}}</li>
                </ul>
                <div class="on"></div>
            </div>
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(12,$event,'month')">
                    <li v-for="i in 12">{{i}}</li>
                </ul>
            </div>
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(day,$event,'day')">
                    <li v-for="i in day">{{i}}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'date-picker',
    data () {
        return {
            day:31,
            currentYear:1993,
            currentMonth:3,
            currentDay:3,
            currentDate:new Date(1993,2,3),
            yearData:{
                defaultY:0,
                value:0
            },
        }
    },
    methods:{
        cancle () {
            this.$emit('cancle');
        },
        sure () {
            this.$emit('sure', this.currentDate);
        },
        ts (e) {
            let $this = this;
            let touchPoint = e.touches[0];
            $this.yearData.defaultY = touchPoint.pageY;
            $this.yearData.value = $this.getTranslateY(e.target.parentNode);
        },
        tm (e) {
            let $this = this;
            let touchPoint = e.touches[0];
            let len = touchPoint.pageY - $this.yearData.defaultY;
            
            e.target.parentNode.style.webkitTransform = "translate3d(0," + ($this.yearData.value+len)+"px,0)";
        },
        te (num,e,type){
            let $this = this;
            let maxY = -(num-3)*36;
            let absValue = Math.abs($this.getTranslateY(e.target.parentNode));
            let temp = absValue%36;
            let absEndValue = temp>18 ? absValue+36-temp : absValue-temp;
            let endValue = $this.getTranslateY(e.target.parentNode)>0 ? absEndValue : -absEndValue;
            if($this.getTranslateY(e.target.parentNode)>72){
                endValue = 72;
            }else if($this.getTranslateY(e.target.parentNode)<maxY){
                endValue = maxY;
            }
            let currentItem = -(endValue/36)+3;
            console.log(currentItem);
            e.target.parentNode.style.webkitTransform = "translate3d(0," + endValue +"px,0)";
            $this.dealFun(e,type,currentItem);
        },
        getTranslateY (dom) {
            let pattern = new RegExp("\\((.| )+?\\)","igm");
            let st = dom.style.webkitTransform;
            if(!st){
                return 0;
            }
            let target = pattern.exec(st)[0];
            return parseInt(target.split(",")[1]);
        },
        dealFun (e,type,currentItem) {
            let $this = this;
            switch(type){
                case "year":(function(){
                    $this.currentYear = 0+e.target.parentNode.childNodes[currentItem-1].innerHTML;
                    $this.setDate();
                })();
                break;
                case "month":(function(){
                    $this.currentMonth = 0+e.target.parentNode.childNodes[currentItem-1].innerHTML;
                    $this.setDate();
                })();
                break;
                case "day":(function(){
                    $this.currentDay = 0+e.target.parentNode.childNodes[currentItem-1].innerHTML;
                    $this.setDate();
                })();
                break;
            }
        },
        setDate () {
            let $this = this;
            let date = new Date($this.currentYear, $this.currentMonth, 0);
            $this.day = date.getDate();
            $this.currentDate = new Date($this.currentYear, $this.currentMonth-1, $this.currentDay);
            console.log($this.currentDate);
        }
    }
}
</script>

<style>
*{
  padding:0;
  margin:0;
  list-style:none;
}
#date-picker{
    position:absolute;
    width:100%;
    top:0;
    bottom:0;
    background-color:rgba(0,0,0,0.5);
}
.header{
    position:absolute;
    bottom:181px;
    display:flex;
    display:-webkit-flex;
    flex-direction:row;
    justify-content: space-between;
    border:1px solid #eee;
}
.header div{
    
    /*width:100%;*/
    height:36px;
    line-height: 36px;
    text-align: center;
    background:#fff;
    box-sizing:border-box;
    -webkit-box-sizing:border-box;
}
.header div:first-child{
    border-right:1px solid #eee;
}
.content {
    width:100%;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    background:#fff;
    height:180px;
    position:absolute;
    bottom:0;
    display:flex;
    display:-webkit-flex;
    flex-direction:row;
    justify-content: space-between;
}
div{
    width:100%;
    overflow:hidden;
}
ul{
    text-align:center;
    transform:translate3d(0,0px,0);
    -webkit-transform:translate3d(0,0px,0);
    transition-duration:.3s;
    -webkit-transition-duration:.3s;
    transition-timing-function:ease-out;
    -webkit-transition-timing-function:ease-out;

}
li{
    height:36px;
    line-height:36px;
}
.on{
    width:100%;
    height:36px;
    position: absolute;
    top:50%;
    margin-top:-18px;
    background: rgba(157, 191, 224,0.5);
}

</style>
