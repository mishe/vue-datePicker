# vue-datePicker
vue日期控件

## 配置

> readonly 输入框只读

> format 日期格式 default: 'YYYY/MM/DD'

> value 默认值 和格式保持一致，默认是当天

> min 最小日期

> max 最大日期

> week-type 周日显示 default:0 周日显示在第一位，1周日显示在最后

> rows 日历显示函数 default:6行

> show-time 显示时间选择 default:false 不显示

> time 时间 default: '00:00'


## 默认控件：
    <datepicker></datepicker>

## 带时间格式控件：YYYY-MM-DD
    <datepicker  format="YYYY-MM-DD" value="2016-12-05" week-type="1"></datepicker>

## 设置周日显示在最后：YYYY-MM-DD

    <datepicker  format="YYYY/MM/DD" value="2016/12/05" week-type="1"></datepicker>


## 设置最大和最小日期：
    <datepicker  format="YYYY/MM/DD" value="2016/12/05" min="2016/12/01" max="2016/12/20"></datepicker>

## 显示时间选择：
    <datepicker show-time="0" format="YYYY/MM/DD" value="2016/12/05" min="2016/12/01" max="2016/12/20" week-type="0"></datepicker>



