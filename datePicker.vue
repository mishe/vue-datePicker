<style>
    .data-picker-box{width:210px;height:240px;background:#fff;border:1px solid #eee;overflow:hidden}
    .data-picker-box .date-picker-head,
    .data-picker-box .week-days{height:30px;line-height:30px;border-bottom:1px solid #eee}
    .data-picker-box .prev-year,
    .data-picker-box .prev-month,
    .data-picker-box .next-month,
    .data-picker-box .next-year{margin-left:6px;text-indent:-99999px}
    .data-picker-box .prev-year:before{content:' << '}
    .data-picker-box .next-year:before{content:' >> '}
    .data-picker-box .prev-month:before{content:' < '}
    .data-picker-box .next-month:before{content:' > '}
    .data-picker-box .cur-year,
    .data-picker-box .date-desc,
    .data-picker-box .month-days-one{display:inline-block;width:100px;text-align:center}
    .data-picker-box .date-desc,
    .data-picker-box .month-days-one{width:30px}
    .data-picker-box .month-days-one:hover{background:#eee}
    .data-picker-box .month-days-one span{display:inline-block;height:30px;width:30px;line-height:30px;cursor:pointer}
    .data-picker-box .grey{color:#666;background:#f5f5f5}
    .data-picker-box .grey:hover{background:#ddd}
    .data-picker-box .selected{background:#abc}
    .data-picker-box .data-time-box{float:right;width:79px;height:240px;overflow:hidden;border-left:1px solid #eee;background:#fff}
    .data-picker-box .times-wrap p{height:25px;line-height:25px;margin:0;text-align:center}
    .data-picker-box .times-wrap p:hover{background:#eee;cursor:pointer}
    .data-picker-box .data-time-box .time-tip{margin:0;height:30px;line-height:30px;border-bottom:1px solid #eee;text-align:center}
    .data-picker-box .data-time-box .times-wrap{height:210px;overflow:auto}
    .data-picker-box.show-times{width:290px}
</style>
<template>
    <div class="data-picker-wrap">
        <input type="text"
                :readonly="readonly"
                :value="value+(showTime?' '+time:'')"
                @click="show = !show">
        <div class="data-picker-box" :class="{'show-times':showTime}" v-show="show">
            <div class="data-time-box" v-show="show && showTime">
                <p class="time-tip">选择时间</p>
                <div class="times-wrap">
                <p v-for="time in times" :class="time.selected" @click="selectTime(time.time)">{{time.time}}</p>
                </div>
            </div>
            <div class="date-picker-head">
                <span class="prev-year" @click="changeYear((+curYear)-1)"></span>
                <span class="prev-month" @click="changeMonth((+curMonth)-1)"></span>
                <span class="cur-year">{{curYear}}-{{curMonth}}</span>
                <span class="next-month" @click="changeMonth((+curMonth)+1)"></span>
                <span class="next-year" @click="changeYear((+curYear)+1)"></span>
            </div>
            <div class="week-days">
                <span class="date-desc" v-for="weekDay in weekDays">{{weekDay}}</span>
            </div>
            <div class="month-days">
                <span class="month-days-one" v-for="day in monthDays" @click="selectDate(day.month,day.day)">
                    <span :class="[day.color,day.selected]">{{day.day}}</span>
                </span>
            </div>

        </div>
    </div>
</template>

<script>
    Vue.component('datepicker', {
        template:'#asss',
        props: {
            readonly: Boolean,
            format: {
                type: String,
                default: 'YYYY/MM/DD'
            },
            value: {
                type: Date,
                default: new Date().getFullYear()+'/'+(new Date().getMonth()+1)+'/'+new Date().getDate()
            },
            min:String,
            max:String,
            weekType:{
                type:Number,
                default:0
            },
            rows:{
                type:Number,
                default:6
            },
            show:{
                type:Boolean,
                default:false
            },
            showTime:{
                type:Boolean,
                default:false
            },
            time:{
                type: String,
                default: '00:00'
            }
        },
        computed:{
            times:function(){
                var times=[],tmp;
                for(var i=0;i<24;i++){
                    tmp=i>9?i+':00':'0'+i+':00';
                    times.push({
                        time:tmp,
                        selected:this.time==tmp?'selected':''
                    });
                    tmp=i>9?i+':30':'0'+i+':30';
                    times.push({
                        time:tmp,
                        selected:this.time==tmp?'selected':''
                    });
                }
                return times;
            },
            curYear:function(){
                return this.value.split(this.format.substr(4,1))[0]
            },
            curMonth:function(){
                return this.value.split(this.format.substr(4,1))[1]
            },
            curDay:function(){
                return this.value.split(this.format.substr(4,1))[2]
            },
            weekDays:function(){
                var d=['一','二','三','四','五','六'];
                this.weekType==0?d.unshift('日'):d.push('日');
                return d;
            },
            monthDays:function(){
                var date,i=0,count,week,days=[];

                //补齐上个月末
                date=new Date(this.curYear,this.curMonth-1,1);
                count=new Date(this.curYear,this.curMonth-1,0).getDate();
                week=6-(6-date.getDay())-this.weekType;
                week=week==0?7:week;
//                console.log(date,week)
                for(;i<week;i++){
                    days.unshift({
                        month:this.curMonth-1,
                        day:count-i,
                        selected:'',
                        color:'grey'
                    });
                }

                date=new Date(this.curYear,this.curMonth,0)
                count=date.getDate();
                for(i=1;i<count+1;i++){
                    days.push({
                        month:this.curMonth,
                        day:i>9?i:'0'+i,
                        selected:this.curDay==i?'selected':'',
                    });
                }

                //补下个月
                week=6-date.getDay()+(+this.weekType);
                days.length+week<this.rows*7 && (week=week+7)
                for(i=1;i<week+1;i++){
                    days.push({
                        month:(+this.curMonth)+1,
                        day:i>9?i:'0'+i,
                        selected:'',
                        color:'grey'
                    });
                }

                return days
            }
        },
        methods: {
            dateFormat:function (y,m,d) {
                var date=new Date(y,m,d),
                    fmt=this.format,
                    o = {
                        "M+": (+date.getMonth()) + 1,
                        "D+": date.getDate(),
                        "h+": date.getHours(),
                        "m+": date.getMinutes(),
                        "s+": date.getSeconds(),
                        "q+": Math.floor((date.getMonth() + 3) / 3),
                        "S": date.getMilliseconds()
                    },
                    format = function (fmt) {
                        if (/(Y+)/.test(fmt))
                            fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
                        for (var k in o)
                            if (new RegExp("(" + k + ")").test(fmt))
                                fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
                        return fmt;
                    };
                return format(fmt);
            },
            selectDate:function(m,d){
                var min,max,cur=this.dateFormat(this.curYear,m-1,d);
                if(this.min){
                    min=new Date(this.min);
                    if(new Date(cur)<min) return false;
                }
                if(this.max){
                    max=new Date(this.max)
                    if(new Date(cur)>max) return false;
                }
                this.value=cur;
                this.show=false;
            },
            changeYear:function(y){
                this.value=this.dateFormat(y,this.curMonth-1,this.curDay);
            },
            changeMonth:function(m){
                this.value=this.dateFormat(this.curYear,m-1,this.curDay);
            },
            selectTime:function(time){
                this.time=time;
                this.show=false;
            }
        }
    });

</script>
