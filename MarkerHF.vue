<template>
  <div>
  <div style="">
    <el-empty v-show="show" description="Waiting for your inquiry"
              v-loading="loading"
              :image-size="320"
              element-loading-text="Calling R to load, please wait patiently"
              element-loading-spinner="el-icon-loading"
              element-loading-background="rgba(0, 0, 0, 0.8)"></el-empty>
    <viewer>
      <el-image
        v-for="img in imgs" :key="img" :src="img" lazy
        v-show="show2"  style=" height: 100%; width: 100%; box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);border-radius: 3px;margin-top: 8px"
        v-loading="loading"
        element-loading-text="Calling R to load, please wait patiently"
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.8)"
      >
      </el-image>
    </viewer>
  </div>
  <div style="  display: flex;justify-content: center;align-items: center;border-top: 1px dashed gray;padding: 10px; ">
    <!--            表单输入然后进行多项搜索-->
    <el-input
      style="margin-right: 10px;margin-left: 10px"
      type="textarea"
      placeholder="Target Gene: Please enter content"
      v-model="value"
      maxlength="380"
      clearable
      show-word-limit
    >
    </el-input>
    <el-select v-model="valuey" placeholder=""  style="margin-top: 6px;height: 50px">
      <el-option
        v-for="item in Y"
        :key="item.value"
        :label="item.value"
        :value="item.value">
      </el-option>
    </el-select>
    <el-button  type="text" icon="el-icon-search" circle @click="click"></el-button>

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
  name: "MarkerHF",
  data(){
    return{
      serverIP:serverIP,
      value:"APOE,PTEN,TP53",
      show:true,
      show2:false,
      loading:false,
      imgs:[],
      Y:[
        { value: "celltype"},
        { value: "celltype_fine"},
        { value: "clusters"},
        { value: "seurat_clusters"},
        { value: "stage_group"},
      ],
      valuey: '',
      RData:"HCC.RData",
      RDatas:[
        { value: "Cirrhotic.RData"},
        { value: "HCC.RData"},
        { value: "Healthy.RData"},
      ],

    }
  },

  methods:{
    click(){
      this.loading=true;
      this.$http.get('http://'+serverIP+':9011/R/draw456', {
          params: {
            gene: this.value,
            y: this.valuey,
            rdata: this.RData
          }
        }
      ).then(res => {
        for (let i = 0; i < res.data.length; i++) {
          this.imgs[i] = res.data[i];
        }
        console.log(res)
        this.loading=false;
        this.show=false
        this.show2=true
      }).catch(err=>{
        // console.log(err)
        this.$message.error(err);
        this.loading=false;
      })
    },


  },

}
</script>

<style scoped>

</style>
