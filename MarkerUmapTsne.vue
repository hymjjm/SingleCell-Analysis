<template>
  <div>
      <el-empty v-show="show7" description="Waiting for your inquiry"
                v-loading="loading7"
                :image-size="320"
                element-loading-text="Calling R to load, please wait patiently"
                element-loading-spinner="el-icon-loading"
                element-loading-background="rgba(0, 0, 0, 0.8)"></el-empty>
      <viewer>
        <el-image
          v-show="show72" :src="img7" style=" height: 100%; width: 100%; box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);border-radius: 3px;margin-top: 8px"
          v-loading="loading7"
          element-loading-text="Calling R to load, please wait patiently"
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
        >
        </el-image>
      </viewer>
      <div style="display: flex;justify-content: center;flex-direction: row;border-top: 1px dashed gray;padding: 10px 10px">
        <el-select
          v-model="value"
          filterable
          allow-create
          default-first-option
          remote
          placeholder="please enter gene symbol"
          :remote-method="remoteMethod">
          <el-option
            v-for="option in options"
            :value="option"
            :key="option">
          </el-option>
        </el-select>
        <el-button  type="text" icon="el-icon-search" circle @click="click7"></el-button>
        <el-select v-model="RData" placeholder="">
          <el-option
            v-for="item in RDatas"
            :key="item.value"
            :label="item.value"
            :value="item.value">
          </el-option>
        </el-select>
      </div>
  </div>

</template>

<script>
import {serverIP} from "../../public/config";

export default {
  name: "MarkerUmapTsne",
  data(){
    return{
      serverIP:serverIP,
      allGenes:[],
      options: [],
      RData:"HCC.RData",
      RDatas:[
        { value: "Cirrhotic.RData"},
        { value: "HCC.RData"},
        { value: "Healthy.RData"},
      ],
      value:"PTEN",
      show7:true,
      show72:false,
      loading7:false,
      img7:"",

    }
  },
  mounted() {
    this.getSelect();//模糊搜索用
  },
  methods:{
    click7(){
      this.loading7=true;
      this.$http.get('http://'+serverIP+':9011/R/draw7', {
          params: {
            gene: this.value,
            rdata: this.RData
          }
        }
      ).then(res => {
        console.log(res)
        this.img7='data:image/png;base64,'+res.data;
        this.picname=this.value+"_"+this.RData +"_"+this.dataname+"_.png";
        this.loading7=false;
        this.show7=false
        this.show72=true
      }).catch(err=>{
        // console.log(err)
        this.$message.error(err);
        this.loading7=false;
      })

    },

    // 模糊搜索
    getSelect() {
      this.$http.get('http://' + serverIP + ':9011/rdk-dlw-drivergene/selectGeneList').then(res => {
        this.allGenes = res.data;
      })
    },
    remoteMethod(query) {
      // 如果用户输入内容了，就发请求拿数据，远程搜索模糊查询
      if (query !== "") {
        this.options = [...new Set(this.allGenes.filter((item) => {
          // 大于-1说明只要有就行，不论是不是开头也好，中间也好，或者结尾也好
          return item.toLowerCase().indexOf(query.toLowerCase()) > -1
        }))]
      } else {
        this.options = [];
      }
    },

  },

}
</script>

<style scoped>

</style>
