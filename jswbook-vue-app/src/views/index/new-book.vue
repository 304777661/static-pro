<template>
    <div class="new-book">
        <headerpage :title_page='title_page="新书通报"' :backBtn='backBtn=true'></headerpage>
        <div v-infinite-scroll="loadMore"
            infinite-scroll-disabled="loading"
            infinite-scroll-distance="10" infinite-scroll-immediate-check="false">
            <div class="borrowing-book-data" v-for="(item,index) in resultList" :key="index">
                <router-link tag="div" :to="{name:'BookDetail',params:{lsh:item.lsh,ssh:item.ssh}}">
                    <div class="book-img"><img src="../../../static/book-img.png" alt=""></div>
                    <ul class="bookimg-rg">
                        <li class="new-book-name">
                            <span>{{item.ztm}}</span>
                            <span></span>
                            <span>
                                <button @click.stop="bookPosition(index)">在馆信息</button>
                                <i v-show="index===setShow"><img src="../../../static/direction-img.png" alt=""></i>
                                <ul class="book-lib-message" v-show="index===setShow">
                                    <li v-for="(itemChild,indexChild) in item.gcd" :key="indexChild">
                                        <span>{{itemChild.gcd}}</span>
                                        <span><font color="ff0000">{{itemChild.number}}</font>本</span>
                                    </li>
                                </ul>
                            </span>
                        </li>
                        <li>
                            <span><font color="#5db3ff">著者：</font></span>
                            <span><font color="#5db3ff">{{item.Dyzrsm}}</font></span>
                        </li>
                        <li>
                            <span>出版社：</span>
                            <span>{{item.Cbsm}}</span>
                            <router-link tag="span" :to="{name:'BookDetail',params:{lsh:item.lsh,ssh:item.ssh,tabSwitch:4}}"><img src="../../../static/comment-pen.png" alt=""> <font color="#666666">{{item.pls.pls || 0}}</font></router-link>
                        </li>
                    </ul>
                </router-link>
            </div>
            <mt-spinner :type="3" v-show="loadingImg"></mt-spinner>
            <div v-show="noneData" class="noDataShow">全部数据已加载</div>
        </div>
    </div>
</template>
<script>
import headerpage from '../../components/header'
// import { Toast } from 'mint-ui'
export default {
    components:{
        headerpage
    },
    created(){
        this.myAjax.postData('tuijian/new_arrivals',
        (result)=>{
            this.totalRow = result.totalRow;
            this.lastPage = result.lastPage;
            this.resultList = result.list;
            if(this.lastPage){
                this.noneData = true;
                this.loadingImg = false;
            } else {
                this.noneData = false;
                this.loadingImg = true;
            }
        },()=>{

        },{});

    },
    mounted(){
        
    },
    data(){
        return {
            bookNumber:2001,
            bookNumberDiv:this.bookNumber,
            setShow:null,
            dropNumber:1,
            lastPage:'',    
            loadingImg:null,
            noneData:'',
            loading:null,
            resultList:[],
            totalRow:''
        }
    },
    methods:{
        bookPosition(a){
            if( this.setShow === a) {
                this.setShow = '';
            }else {
                this.setShow = a;
            }
        },
        loadMore(){
            if(this.lastPage){
                this.noneData = true;
                this.loadingImg = false;
                return;
            } 
            let that = this;
            this.loadingImg = true;
            this.loading = true;
            setTimeout(() => {
            this.myAjax.postData('tuijian/new_arrivals',
            function(result){
                result.list.forEach(element => {
                    that.resultList.push(element);
                });
                that.lastPage = result.lastPage;
                that.loading = false;
            },function(){

            },{fsrq:this.dateSearch,title:this.inputText,pageIndex:++this.dropNumber},that);
            },500);
        }
    }
}
</script>
<style lang="scss">
div.new-book {
    background-color: #efefef;
    div.borrowing-book-data {
        padding: 23px;
        background-color: #fff;
        border-bottom: 1px solid #dddddd;
        >div {
            display: flex;
            align-items: center;
            .book-img {
                border-radius: 4px;
                background-color: rgb(171, 171, 171);
                width: 96px;
                height: 120px;
                overflow: hidden;
                >img {
                    width: 142px;
                    height: 184px;
                }
            }
            .bookimg-rg {
                margin-left: 24px;
                flex-grow: 1;
                height: 120px;
                display: flex;
                flex-direction: column;
                // justify-content: space-around;
                >li {
                    height: 34px;
                    line-height: 34px;
                    font-size: 24px;
                    >span:nth-of-type(1) {
                        
                        color: #333333;
                    }
                    >span:nth-of-type(2) {
                        color: #666666;
                    }
                    >span:nth-of-type(3) {
                        position: relative;
                        float: right;
                        >button {
                            border-width: 1px;
                            border-color: rgb(11, 127, 229);
                            border-style: solid;
                            border-radius: 6px;
                            padding: 0 8px;
                            height: 38px;
                            line-height: 36px;
                            text-align: center;
                            font-size: 24px;
                            color: rgb(11, 127, 229);
                            background-color: transparent;
                        }
                        i {
                            position: absolute;
                            bottom: -3px;
                            width: 13px;
                            height: 13px;
                            left: 50%;
                            margin: -6px;
                            img {
                                width: 13px;
                                height: 13px;
                            }
                        }
                    }
                    img {
                        width: 24px;
                        height: 24px;
                        vertical-align: text-top;
                    }
                }
                >li.new-book-name {
                    height: 55px;
                    >span:nth-of-type(1) {
                        width: 450px;
                        white-space: nowrap;
                        text-overflow: ellipsis;
                        overflow: hidden;
                        display: inline-block;
                        font-size: 32px;
                        color: #333333;
                    }
                }
            }
        }
    }
}
.book-lib-message {
    position: absolute;
    border-radius: 8px;
    right: 0;
    top: 38+13px;
    background-color: #efefef;
    width: 260px;
    z-index: 2;
    overflow: hidden;
    >li {
        height: 60px;
        line-height: 60px;
        border-bottom: 1px solid #cccccc;
        display: flex;
        justify-content: space-between;
        padding: 0 27px;
    }
}
</style>
