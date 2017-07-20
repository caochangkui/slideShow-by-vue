<template>
    <div class="slide-show" @mouseover="clearInv" @mouseout="runInv">
        <div class="slide-img">
            <a :href="slides[nowIndex].href">
                <transition name="slide-trans">
                    <img v-if="isShow" :src="slides[nowIndex].src">
                </transition>
                <transition name="slide-trans-old">
                    <img v-if="!isShow" :src="slides[nowIndex].src">   <!-- 两个img中的nowIndex是不同的，第二个在setTimeout中已经变掉了 -->
                </transition>
            </a>
        </div>
        <h2>{{slides[nowIndex].title}}</h2>
        <ul class="slide-pages">
            <li @click="goto(prevIndex)">&lt;</li>
            <li v-for="(item,index) in slides" @click="goto(index)">
                <a :class="{on: index === nowIndex}">{{index + 1}}</a>
            </li>
            <li @click="goto(nextIndex)">&gt;</li>
        </ul>
    </div>
</template>

<script>
export default {
    name: 'slideShow',
    props: {
        slides: {
            type: Array,
            default: []
        },
        inv: {
            type: Number,
            default: []
        }
    },
    data () {
        return {
            nowIndex: 0,
            isShow: true,
            invId: ''   //原文里面没有写这一行
        }
    },
    computed: {   //使用计算属性，将上一页和下一页与nowIndex向关联
        prevIndex(){
            if(this.nowIndex === 0){
                return this.slides.length - 1
            }else{
                return this.nowIndex - 1
            }
        },
        nextIndex(){
            if(this.nowIndex === this.slides.length - 1){
                return 0
            }else{
                return this.nowIndex + 1
            }
        }
    },
    methods: {
        goto(index){
            this.isShow = false
            setTimeout(()=>{
                this.isShow = true
                this.nowIndex = index
            }, 10)
        },
        runInv(){//幻灯片自动轮播
            this.invId = setInterval(() => {
                this.goto(this.nextIndex)
            }, this.inv)
        },
        clearInv(){
            clearInterval(this.invId)
        }
    },
    mounted(){//组件周期函数，完毕后执行
        this.runInv()
    }
}
</script>


<style scoped>
.slide-trans-enter-active {
  transition: all .5s ease;
}
.slide-trans-enter {
  transform: translateX(900px); /*进入函数，从右侧偏900这个状态向0状态过渡*/
}
.slide-trans-old-leave-active {
  transition: all .5s ease;
  transform: translateX(-900px);/*离开函数，从左侧偏900这个状态向0状态过渡*/
}
.slide-show {
  position: relative;
  margin: 15px 15px 15px 0;
  width: 900px;
  height: 500px;
  overflow: hidden;
}
.slide-show h2 {
  position: absolute;
  width: 100%;
  color: #fff;
  font-size: 16px;
  background: #000;
  opacity: .7;
  bottom: 0;
  height: 30px;
  line-height: 30px;
  text-align: left;
  padding-left: 15px;
}
.slide-img {
  width: 100%;
}
.slide-img img {
  width: 100%;
  position: absolute;
  top: 0;
}
.slide-pages {
  position: absolute;
  bottom: 10px;
  right: 15px;
}
.slide-pages li {
  display: inline-block;
  padding: 0 10px;
  cursor: pointer;
  color: #fff;
  font-size: 16px;
}
.slide-pages li .on {
  text-decoration: underline;
  color: red;
}
</style>
