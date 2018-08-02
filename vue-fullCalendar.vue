<template lang='html'>
    <div class='fullCalenDar'>
       <div>
           <div class="btn_div">
               <div class="btn_group">
                <button id="last-week" @click="lastWeek">上一周</button>
                <button id="next-week" @click="nextWeek">下一周</button>
            </div>
           </div>
            <table id="monitor">
                <thead>
                    <tr>
                        <td>{{fullCalenDarData.leftTiTle}}</td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                    </tr>
                </thead>
                <tbody>
                      <tr v-for="(item,index) in fullCalenDarData.tableData">
                        <td>{{fullCalenDarData.leftConfig[index]}}</td>
                        <td v-for="obj in item">
                            <div>
                                <div @click="addEvent(item,index,$event)" class="addEvent">添加事件</div>
                                <el-tooltip  v-for="(element,elementIndex) in listFilter(obj)" class="event" placement="right">
                                    <div slot="content">
                                        {{element.title}}
                                    </div>
                                    <div @click="editEvent({
                                            item:item,
                                            element:element,
                                            obj:obj,
                                            index:index,
                                            elementIndex:elementIndex,
                                            $event:$event
                                        })"  :style="{background:element.bg,color:element.color=='undefind'?'#000':element.color}" >{{element.title}}</div>
                                </el-tooltip>
                                <div v-if="obj.length>=(fullCalenDarData.limit)&&isMore==false" class="more"  @click="more()">更多</div>
                                <div v-else-if="obj.length>=(fullCalenDarData.limit)" @click="shrink()" class="more">收缩</div>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
            <el-dialog
            :title="dialogData.title"
            :visible.sync="dialogData.dialogVisible"
            width="30%"
            :before-close="handleClose">
            <span v-if="editStatus==1">
                添加
            </span>
            <span v-else-if="editStatus==2">
                修改
            </span>
            <span slot="footer" v-if="editStatus==1" class="dialog-footer">
                <el-button @click="dialogData.dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogData.dialogVisible = false">确 定</el-button>
            </span>
            <span slot="footer" v-else-if="editStatus==2" class="dialog-footer">
                <el-button @click="dialogData.dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogData.dialogVisible = false">修改</el-button>
            </span>
            </el-dialog>
       </div>
    </div>
</template>
<script>
     export default {
        name: 'demo',
        data () {
            return{
                dialogData:{
                    dialogVisible:false,
                    title:"提示"
                },
                isMore:false,
                editStatus:1,//'1'添加,'2'修改
                fullCalenDarData:{
                    leftTiTle:"时间视图",
                    container:"monitor",
                    currentFirstDate:"",
                    leftConfig:["8:00","9:00"],
                    limit:4,//td里面事件最多4个
                    tableData:[
                        [
                            [
                                {
                                    title:"第一",
                                    bg:"red",
                                    color:"#fff"
                                },
                                {
                                    title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                                 {
                                     title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                                 {
                                     title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                                 {
                                     title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                            ],
                            [
                                {
                                     title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                                 {
                                     title:"第一",
                                    bg:"red",
                                    color:"#fff",
                                },
                            ],
                            [],
                            [],
                            [],
                            [],
                            []
                        ],
                    ]
                }
            }
        },
        created(){
        },
        mounted(){
          this.init()
        },
        computed:{
        },
        methods:{
            init(){
                this.fullCalenDarData.currentFirstDate="";
                //根据今天计算这周   
                this.setWeekDate(new Date());
            },
            addDate(date,n){
                //设置某天
                date.setDate(date.getDate()+n); 
                return date;
            },
            moreFilter(){
               this.isMore=true;
            },
            listFilter(element){
                console.log(element)
                if(this.isMore==true){
                    return element;
                }else{
                    if(element.length>this.fullCalenDarData.limit){
                        let len=element.length-this.fullCalenDarData.limit;
                        let obj=JSON.parse(JSON.stringify(element));
                        obj.splice(this.fullCalenDarData.limit-1,len);
                        return obj;
                    }else{
                        return element;
                    }
                }
            },
            lastWeek(){
                //上一周
                this.setWeekDate(this.addDate(this.fullCalenDarData.currentFirstDate,-7));     
            },
            nextWeek(){
                //下一周
                this.setWeekDate(this.addDate(this.fullCalenDarData.currentFirstDate,7));
            },
            setWeekDate(date){
                //设置一周       
                var week = date.getDay();
                date = this.addDate(date,week*-1);
                this.fullCalenDarData.currentFirstDate = new Date(date);
                // var cells = document.getElementById(this.fullCalenDarData.container).getElementsByTagName('td');
                var cells = document.querySelectorAll(`#${this.fullCalenDarData.container} thead tr td`);
                var clen = cells.length;
                for(var i = 0;i<clen;i++){
                    if(i!=0){
                        cells[i].innerHTML =this.formatDate(i==0 ? date : this.addDate(date,1));
                        cells[i].setAttribute("date",this.formatDate(i==0 ? date : this.addDate(date,1)));
                    }
                }        
            },
            formatDate(date){
                //一周时间格式化       
                var year = date.getFullYear()+'年';
                var month = (date.getMonth()+1)+'月';
                var day = date.getDate()+'日';
                var week = '('+['星期天','星期一','星期二','星期三','星期四','星期五','星期六'][date.getDay()]+')'; 
                return year+month+day+' '+week;
            },
            addEvent(item,index,$event){
                console.log($event.target.innerText)
                this.dialogData.dialogVisible=true;
                var cells=document.querySelectorAll(`#${this.fullCalenDarData.container} thead tr td`);
                this.dialogData.title=cells[index+1].innerText+"   "+$event.target.innerText;
                 this.editStatus=1;
                console.log(cells[index+1].innerText)
            },
            editEvent({
                        item,
                        element,
                        obj,
                        index,
                        elementIndex,
                        $event
                    }){
                this.dialogData.dialogVisible=true;
                var cells=document.querySelectorAll(`#${this.fullCalenDarData.container} thead tr td`);
                this.dialogData.title=cells[index+1].innerText+"   "+$event.target.innerText;
                this.dialogData.dialogVisible=true;
                this.editStatus=2;
            },
            more(){
                // this.dialogData.title="更多";
                // this.dialogData.dialogVisible=true;
               
                this.isMore=true;
            },
            shrink(){
                this.isMore=false;
            },
            handleClose(){

            }
        }
    }
</script>
<style lang="scss" scoped>
    .fullCalenDar{
        .btn_div{
            width: 1400px;
            padding-bottom: 10px;
            overflow: hidden;
            margin: 0 auto;
            .btn_group{
            float: right;
                button{
                    background: #fff;
                    padding: 10px;
                    border:1px solid gray;
                    cursor: pointer;
                }
                button:nth-of-type(1){
                    border-right:0;
                }
            }
        }
        #monitor{
            overflow: hidden;
            line-height: 25px;
            text-align: center;
            border-collapse: collapse;
            padding: 2px;
            width: 1400px;
            margin: 0 auto;
            td{
                border:1px solid #ddd;
                padding: 10px;
                .addEvent{
                    background: #dedede;
                    cursor: pointer;
                    margin-bottom:10px;
                }
                .addEvent:last-child{
                    margin: 0;
                }
                .event{
                    margin-bottom: 10px;
                    cursor: pointer;
                }
                .event:nth-last-child(1){
                    margin: 0;
                }
                .more{
                    cursor: pointer;
                    background: gray;
                }
            }
        }
    }
    
</style>