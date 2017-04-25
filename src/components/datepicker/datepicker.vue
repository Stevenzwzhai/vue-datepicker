<template>
    <div id="date-picker">
        <div class="header">
            <div @click="cancle">取消</div>
            <div @click="sure">确定</div>
        </div>
        <div class="content">
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(30,$event,'year')" ref="year">
                    <li v-for="(i, index) in 30" :index="index">{{1990+i}}</li>
                </ul>
                <div class="on"></div>
            </div>
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(12,$event,'month')" ref="month">
                    <li v-for="(i, index) in 12" :index="index">{{i}}</li>
                </ul>
            </div>
            <div>
                <ul @touchstart="ts" @touchmove="tm" @touchend="te(day,$event,'day')" ref="day">
                    <li v-for="(i, index) in day" :index="index">{{i}}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'date-picker',
    mounted () {
        let olddate = null;
        if(!this.selecteddate){
            olddate = new Date();
        }else{
            olddate = this.selecteddate;
        }
        this.moveLength=0;
        this.currentYear = parseInt(olddate.getFullYear());
        this.currentMonth = parseInt(olddate.getMonth());
        this.currentDay = parseInt(olddate.getDate());
        this.currentDate = olddate;
        this.currentItem.year = this.currentYear-1990;
        this.currentItem.month = this.currentMonth+1;
        this.currentItem.day = this.currentDay;
        this.translateYFun(this.$refs.year, -(this.currentYear-1993)*36);
        this.translateYFun(this.$refs.month, -(this.currentMonth-2)*36);
        this.translateYFun(this.$refs.day, -(this.currentDay-3)*36);
    },
    data () {
        return {
            day:31,
            currentYear:1991,
            currentMonth:1,
            currentDay:1,
            currentDate:new Date(1991,0,1),
            yearData:{
                defaultY:0,
                value:0
            },
            moveLength:0,
            currentItem:{
                'year':1,
                'month':2,
                'day':1
            }
        }
    },
    props:['selecteddate'],
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
            $this.moveLength = (touchPoint.pageY - $this.yearData.defaultY)*2;
            $this.translateYFun(e.target.parentNode, $this.yearData.value+$this.moveLength);
            // e.target.parentNode.style.webkitTransform = "translate3d(0," + ($this.yearData.value+$this.moveLength)+"px,0)";
        },
        te (num,e,type){
            let $this = this;
            let maxY = -(num-3)*36;
            let absValue = Math.abs($this.getTranslateY(e.target.parentNode));
            let temp = absValue%36;
            let absEndValue = temp>18 ? absValue+36-temp : absValue-temp;
            let endValue = $this.getTranslateY(e.target.parentNode)>0 ? absEndValue : -absEndValue;
            let touchedItem = parseInt(e.target.getAttribute('index'))+1;
            if($this.getTranslateY(e.target.parentNode)>72){
                endValue = 72;
            }else if($this.getTranslateY(e.target.parentNode)<maxY){
                endValue = maxY;
            }else if($this.moveLength==0){
                endValue = $this.getTranslateY(e.target.parentNode)+(-touchedItem+$this.currentItem[type])*36;
            }
            $this.currentItem[type] = -(endValue/36)+3;
            $this.translateYFun(e.target.parentNode, endValue);
            // e.target.parentNode.style.webkitTransform = "translate3d(0," + endValue +"px,0)";
            //重置为0，方便下一次判断是否为滑动
            $this.moveLength=0;
            $this.dealFun(e,type,$this.currentItem[type]);
        },
        getTranslateY (dom) {
            let pattern = new RegExp("\\((.| )+?\\)","igm");
            let st = window.getComputedStyle(dom,null).getPropertyValue("-webkit-transform");
            if(!st){
                return 0;
            }
            let target = pattern.exec(st)[0];
            return parseInt(target.split(',')[5].split(')')[0]);
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
        },
        translateYFun (dom, value) {
            dom.style.webkitTransform = "translate3d(0," + value +"px,0)";
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
    transform:translate3d(0,72px,0);
    -webkit-transform:translate3d(0,72px,0);
    transition-duration:.2s;
    -webkit-transition-duration:.2s;
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
