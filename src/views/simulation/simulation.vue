<template>
  <div class="content">
      
      <!-- <tabbar></tabbar> -->
    <!-- <router-view></router-view> -->
    <!-- <h2>1</h2> -->
    <input placeholder="车牌号" class="input" value="" id="pai"/>
    <div>
      <model class="btn" @jinClick="jinClick" @chuClick="chuClick"></model>
    </div>
    
    <list class="list" :pu="pu" :zhuan="zhuan"></list>

    <a-row :gutter="16" >
    <a-col :span="12">
      <a-statistic-countdown
        title="Countdown"
        :value="deadline"
        style="margin-right: 50px"
        @finish="onFinish"
      />
    </a-col>
    <a-col :span="12">
      <a-statistic-countdown
        title="Million Seconds"
        :value="deadline"
        format="HH:mm:ss:SSS"
        style="margin-right: 50px"
      />
    </a-col>
    <a-col :span="24" style="margin-top: 32px;">
      <a-statistic-countdown title="Day Level" :value="deadline" format="D 天 H 时 m 分 s 秒" />
    </a-col>
  </a-row>

  <div :class="{line:time}" class="fei" >
    <a-button size='large' :block='true' @click="btnClick">
      缴费
    </a-button>
  </div>
  
  </div>
</template>

<script>
import tabbar from '../../components/tabbar.vue'
import model from './model.vue'
import list from './list.vue'
import {getCar,parkfind,logenter,logleave} from '../../network/home'
export default {
    name:'simulation',
    components: {
    tabbar,
    model,
    list,
  },
  data(){
    return{
      zhuan:0,
      pu:0,
      pai:'',
      data1:[],
      data2:[],
      deadline: 0,
      time:true
    }
  },
  created(){
    parkfind().then(res=>{
        let ju=[...res];
        console.log('ju',ju.length);
        for (let item of ju) {
            if(item.is_fixed)
                {this.zhuan++}
            else
                {this.pu++}
            }
        })
  },
  updated(){
    this.pai=document.getElementById('pai').value;
  },
  methods:{
    onFinish() {
      console.log('finished!');
    },
    jinClick(){
      // alert('jin');
      // this.zhuan-=1
      // this.pu-=1
      this.pai=document.getElementById('pai').value;
      logenter({id_num:this.pai}).then(res => {
            console.log('res', res);
        }).catch(err => {
            console.log('err')
        })
    },
    chuClick(){
      // alert('jin')
      // this.zhuan+=1
      // this.pu+=1
      // this.pai=document.getElementById('pai').value;
      // console.log((this.pai));
      this.pai=document.getElementById('pai').value;
      logleave({id_num:this.pai}).then(res => {
            console.log('res', res.bill);
            alert('请在5分钟之内缴费'+res.bill+'元')
            this.deadline=Date.now()+10000 * 30;

            this.time=false

        }).catch(err => {
            console.log('err')
        })
      
    },
    btnClick(){
      alert('缴费成功');
      this.time=true;
      this.deadline=0;
    }
  }
}
</script>

<style scoped>
    .content{
        position: absolute;
        left: 30%;
        top: 120px;
        float: right;
    }
    .input{
        left: 0;
        height: 50px;
        width: 500px;
    }
    .btn{
      float: left;
      padding-top: 100px;
      left: 0;
    }
    .list{
      padding-top: 200px;
    }
    .line{
      display: none;
    }
    .fei{
      font-size: 30px;
      height: 200px;
      width: 200px;
    }
</style>