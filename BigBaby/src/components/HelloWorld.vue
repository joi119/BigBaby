<template>
  <div class="hello">
    <div id="menu" :class="[ issactive ? '' :'sshow']">
      <div class="title">
        <span>请先登录大宝贝</span>
      </div>
      <div class="title-msg">
        <span>请输入登录账户和密码</span><br>
        <span>登陆账户：031902641</span>
        <span>登陆密码：ilovebigbaby</span>
      </div>
      <form class="login-form">
        <!--输入框-->
        <div class="input-content">
          <div>
            <input v-model="username" type="text" autocomplete="off"
                   placeholder="用户名" name="userNameOrEmailAddress" required/>
          </div>
          <div style="margin-top: 16px">
            <input v-model="userpwd" type="password"
                   autocomplete="off" placeholder="登录密码" name="password" required maxlength="32"/>
          </div>
        </div>
        <!--登入按钮-->
        <div style="text-align: center">
          <button class="enter-btn" @click="login()">登录</button>
        </div>
        <p :class="[isp?'ispshow':'']" style="color:#9b0000;margin-left: -195px;font-size:13px;">请输入正确的用户名和密码</p>
        <div class="foot">
          <div class="left"><span>忘记密码 ?</span></div>
          <div class="right"><span>注册账户</span></div>
        </div>
      </form>
    </div>
<!--------------------------右边大宝贝------------------------------------->
    <div id="uploader" :class="[ issactive ? 'sshow' :'']">
      <div id="logo">
        <img  src="../assets/logo.png" height="150" width="92"/>
      </div>
      <div id="func">
        <p id="p1">大宝贝助手</p>
        <div>
          <!-- 三元表达式 注意点：放在数组中，类名要用引号-->
          <p id="p2" :class="[ isactive ? '' :'show']">选择要上传的文件</p>
          <!--------------------------文件们------------------------------------------->
          　　<table id="allfile" :class="[ isactive ? 'show' :'']">
          　　　　<tr id="col">
          　　　　　　<th class="file_name">文件名</th>
          <!--　　　　　　<th class="file_size">大小</th>-->
          <!--　　　　　　<th class="state">状态</th>-->
          　　　　　　<th>操作</th>
          　 　　 </tr>
                  <tr :class="[ isshow1 ? 'show' :'']" v-for="(item,index) in files" :key="index">
            　　　　　　<td>{{item.filename}}</td>
            <!--　　　　　　<td>{{(item.filesize/1024).toFixed(1)}}kb</td>-->
            <!--　　　　　　<td>等待上传</td>-->
            　　　　　　<td><button @click="del_files(index)" class="del_button">删除</button></td>
            　 　　</tr>
                   <tr :class="[ isshow2 ? 'show' :'']" v-for="(item,index) in photofiles" :key="index">
            　　　　　　<td>{{item.photofilename}}</td>
            <!--　　　　　　<td>{{(item.photofilesize/1024).toFixed(1)}}kb</td>-->
            <!--　　　　　　<td>等待上传</td>-->
                        <td><button @click="del_photofiles(index)" class="del_button">删除</button></td>
                        <td><button @click="dialogVisible = true;finish2()" class="crop_button">裁剪</button></td>
            　　　　　　
            　 　　</tr>
          　　</table>
          <!----------------------------按钮们----------------------------------------->
        </div>
        <div id="input_parent">
          <button id="choose">选择文件</button><br>
          <input type="file" id="input_file" @change="getFile($event)" multiple="multiple"/><br>
          <button id="upload">上传文件</button>
        </div>
      </div>
    </div>
    <!----------------------------------裁剪框--------------------------------------------------->
    <el-dialog
      title=""
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">

      <div class="content">
        <div class="show-info">
          <div class="test">
            <vueCropper ref="cropper2" :img="example2.img " :outputSize="example2.size"
                        :outputType="example2.outputType"
                        :info="example2.info" :canScale="example2.canScale"
                        :autoCrop="example2.autoCrop"
                        :autoCropHeight="example2.autoCropHeight" :fixed="example2.fixed"
                        :autoCropWidth="example2.autoCropWidth"
                        :fixedNumber="example2.fixedNumber" :enlarge="4">
            </vueCropper>
          </div>
          <button @click="finish2()" class="btn">裁剪</button>
        </div>
      </div>

    </el-dialog>

  </div>
</template>

<script>
  import { VueCropper } from 'vue-cropper'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      username:'',
      userpwd:'',
      isp:true,
      isactive:true,
      issactive:true,
      isshow1:true,
      isshow2:true,
      files:[{
        filename:'',
        filesize:'',
        uploadPercentage: '',
      }],
      photofiles:[{
        photofilename:'',
        photofilesize:'',
        photouploadPercentage: '',
      }],
      number:0,
      number1:0,
      number2:0,
      flag1:1,
      flag2:1,
      ////////////////////////
      dialogVisible: false,
      model: false,
      modelSrc: '',
      crap: false,
      previews: {},
      form: {
        head: ''
      },
      example2: {
        //img的路径自行修改
        img: '$oss.url + \'/\' + form.head ',
        info: true,
        size: 1,
        outputType: 'jpeg',
        canScale: true,
        autoCrop: true,
        // 只有自动截图开启 宽度高度才生效
        autoCropWidth: 300,
        autoCropHeight: 250,
        fixed: true,
        // 真实的输出宽高
        infoTrue: true,
        fixedNumber: [4, 3]
      },
      downImg: '#',
    }
  },
  methods:{
    login(){
      const that = this;
      if(that.username==='031902641'&&that.userpwd==='ilovebigbaby'){
        that.issactive=false;
      }else{
        that.isp=false
      }
    },
    getFile:function(e) {
      const that=this;
      window.file = e.target.files;
      that.isactive = false;
      for(let i=0;i<file.length;i++){
        let str = file[i].name;
        let type=str.split('.', str.length)[1];
        if(type==='jpg'||type==='jpeg'||type==='png'||type==='gif'){
          that.number2++;
          console.log("num2="+that.number2);
          if(that.flag2===0){//非第一个图片文件
            that.photofiles.splice(that.number2,1,{
              photofilename:file[i].name,
              photofilesize:file[i].size,
              photouploadPercentage: 67,
            });
            //上传图片
            this.example2.img = '';
            let thisfile = file[i];
            let reader = new FileReader();
            reader.onload = (e) => {
              let data
              data = reader.result;
              console.log(data);
              if (typeof reader.result === 'object') {
                // 把Array Buffer转化为blob 如果是base64不需要
                data = window.URL.createObjectURL(new Blob([reader.result]))
              } else {
                data = reader.result
              }
              this.example2.img = data
            }
            reader.readAsArrayBuffer(thisfile)
          }
          if(that.flag2===1){//第一个图片文件
            that.photofiles.splice(0,1,{
              photofilename:file[i].name,
              photofilesize:file[i].size,
              photouploadPercentage: 67,
            });
            that.flag2=0;
          }
          //上传图片
          this.example2.img = '';
          let thisfile = file[i];
          let reader = new FileReader();
          reader.onload = (e) => {
            let data
            data = reader.result;
            console.log(data);
            if (typeof reader.result === 'object') {
              // 把Array Buffer转化为blob 如果是base64不需要
              data = window.URL.createObjectURL(new Blob([reader.result]))
            } else {
              data = reader.result
            }
            this.example2.img = data
          }
          reader.readAsArrayBuffer(thisfile)
        }
        else{
          that.number1++;
          console.log("num1="+that.number1);
          if(that.flag1===0){//非第一个非图片文件
            that.files.splice(that.number1,1,{
              filename:file[i].name,
              filesize:file[i].size,
              uploadPercentage: 67,
            })
          }
          if(that.flag1===1){//第一个非图片文件
            that.files.splice(0,1,{
              filename:file[i].name,
              filesize:file[i].size,
              uploadPercentage: 67,
            });
            that.flag1=0;
          }
        }
      }
      if(that.number1!==0){
        that.isshow1=false;
      }
      if(that.number2!==0){
        that.isshow2=false;
      }
    },
    del_files(index){
      let that = this;
      that.files.splice(index,1);
      that.number1--;
      that.number=that.number1+that.number2;
      // console.log(that.number);
      if(that.number===0){
        that.isactive = true;
      }
    },
    del_photofiles(index){
      let that = this;
      that.photofiles.splice(index,1);
      that.number2--;
      that.number=that.number1+that.number2;
      // console.log(that.number);
      if(that.number===0){
        that.isactive = true;
      }
    },
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
        })
        .catch(_ => {});
    },
    //点击裁剪，这一步是可以拿到处理后的地址
    finish2() {
      this.$refs.cropper2.getCropData((data) => {
        this.modelSrc = data;
        this.model = false;
        //裁剪后的图片显示
        this.example2.img = this.modelSrc;
      })
    },
    // base64转blob
    toBlob(ndata) {
      //ndata为base64格式地址
      console.log(ndata)
      let arr = ndata.split(','),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }
      return new Blob([u8arr], {
        type: mime
      })
    }
  },
  mounted(){

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .hello {
    text-align: center;
  }
  #menu {
    padding-left: 25px;
    padding-right: 25px;
    padding-top: 15px;
    width: 350px;
    height: 350px;
    background: #FFFFFF;
    margin: auto;
  }
  .title {
    width: 100%;
    height: 40px;
    line-height: 40px;
  }
  .title span{
    font-size: 18px;
    color: #353f42;
  }
  .title-msg {
    width: 100%;
    height: 64px;
    /*line-height: 64px;*/
  }
  .title:hover{
    cursor: default	;
  }
  .title-msg:hover{
    cursor: default	;
  }
  .title-msg span {
    font-size: 12px;
    color: #707472;
  }
  .input-content {
    width: 100%;
    height: 120px;
  }
  .input-content input {
    width: 330px;
    height: 40px;
    border: 1px solid #dad9d6;
    background: #ffffff;
    padding-left: 10px;
    padding-right: 10px;
  }
  .enter-btn {
    width: 350px;
    height: 40px;
    color: #fff;
    background: #0aa3b8;
    line-height: 40px;
    text-align: center;
    border: 0px;
  }
  .foot{
    width: 100%;
    height: auto;
    color: #9b9c98;
    font-size: 12px;
    margin-top: 20px;
  }
  .enter-btn:hover {
    cursor:pointer;
    background: #168897;
  }
  .foot div:hover {
    cursor:pointer;
    color: #484847;
    font-weight: 600;
  }
  .left{
    float: left;
  }
  .right{
    float: right;
  }
  .ispshow{
    display: none;
  }



  #uploader{
    margin: auto;
    width: 340px;
  }
  #logo{
    margin: 0;
    background-color: #313131;
    padding: 50px 30px 70px 30px;
    width: 280px;
    border-top-left-radius: 25px;
    border-top-right-radius: 25px;
  }
  #func{
    margin: 0;
    width: 340px;
  }
  #p1{
    color: #1e3938;
    margin-top: 35px;
  }
  #p2{
    color: #46b2d6;
    margin-top: 40px;
    font-size: 12px;
    /*display:none*/
  }
  .del_button{
    text-align: center;
    font-size: 8px;
    background-color: #60a9c1;
    border: none;
    color: white;
    border-radius: 8px;
    cursor: pointer;
    transition:background-color 0.25s;
    -moz-transition:background-color 0.25s; /* Firefox 4 */
    -webkit-transition:background-color 0.25s; /* Safari and Chrome */
    -o-transition:background-color 0.25s; /* Opera */
  }
  .del_button:hover{
    background-color: #528da4;
  }
  .del_button:active{
    box-shadow:0px 0px 10px #313131;
  }
  .crop_button{
    text-align: center;
    font-size: 8px;
    background-color: #60a9c1;
    margin-left: -5px;
    border: none;
    color: white;
    border-radius: 8px;
    cursor: pointer;
    transition:background-color 0.25s;
    -moz-transition:background-color 0.25s; /* Firefox 4 */
    -webkit-transition:background-color 0.25s; /* Safari and Chrome */
    -o-transition:background-color 0.25s; /* Opera */
  }
  .crop_button:hover{
    background-color: #528da4;
  }
  .crop_button:active{
    box-shadow:0px 0px 10px #313131;
  }
  #choose{
    position: absolute;
    margin:auto;
    top: 30px;
    left: 80px;
    background-color: #60a9c1;
    border: none;
    color: white;
    padding: 15px 55px;
    text-align: center;
    font-size: 20px;
    border-radius: 25px;
    cursor: pointer;
    transition:background-color 0.25s;
    -moz-transition:background-color 0.25s; /* Firefox 4 */
    -webkit-transition:background-color 0.25s; /* Safari and Chrome */
    -o-transition:background-color 0.25s; /* Opera */
  }
  #choose:hover{
    background-color: #528da4;
  }
  #choose:active{
    box-shadow:0px 0px 10px #313131;
  }
  .show{
     display: none;
  }
  .sshow{
    display: none;
  }
  #allfile{
    width:100%;
    font-size: 15px;
    margin: auto;
  }
  .crooper{
    display:none;
  }
  #input_parent{
    position: relative;
  }
  #input_file{
    opacity: 0%;
    position: absolute;
    z-index: 100;
    top: 50px;
    left: 100px;
    /*display: none;*/
    width: 140px;
    cursor: pointer;
  }
  #upload{
    position: absolute;
    margin:auto;
    top: 120px;
    left: 80px;
    background-color: #5bc189;
    border: none;
    color: white;
    padding: 15px 55px;
    text-align: center;
    font-size: 20px;
    border-radius: 25px;
    cursor: pointer;
    transition:background-color 0.25s;
    -moz-transition:background-color 0.25s; /* Firefox 4 */
    -webkit-transition:background-color 0.25s; /* Safari and Chrome */
    -o-transition:background-color 0.25s; /* Opera */
  }
  #upload:hover{
    background-color: #448f65;
  }
  #upload:active{
    box-shadow:0px 0px 10px #313131;
  }
  /*---------------------------------------------------------------------*/
  .content {
    margin: auto;
    max-width: 585px;
    margin-bottom: 100px;
  }
  .test-button {
    display: flex;
    flex-wrap: wrap;
  }
  .btn {
    display: inline-block;
    line-height: 1;
    white-space: nowrap;
    cursor: pointer;
    background: #fff;
    border: 1px solid #c0ccda;
    color: #1f2d3d;
    text-align: center;
    box-sizing: border-box;
    outline: none;
    margin: 20px 10px 0px 0px;
    padding: 9px 15px;
    font-size: 14px;
    border-radius: 4px;
    color: #fff;
    background-color: #50bfff;
    border-color: #50bfff;
    transition: all .2s ease;
    text-decoration: none;
    user-select: none;
  }

  .des {
    line-height: 30px;
  }

  code.language-html {
    padding: 10px 20px;
    margin: 10px 0px;
    display: block;
    background-color: #333;
    color: #fff;
    overflow-x: auto;
    font-family: Consolas, Monaco, Droid, Sans, Mono, Source, Code, Pro, Menlo, Lucida, Sans, Type, Writer, Ubuntu, Mono;
    border-radius: 5px;
    white-space: pre;
  }

  .show-info {
    margin-bottom: 50px;
  }

  .show-info h2 {
    line-height: 50px;
  }

  /*.title, .title:hover, .title-focus, .title:visited {
        color: black;
    }*/

  .title {
    display: block;
    text-decoration: none;
    text-align: center;
    line-height: 1.5;
    margin: 20px 0px;
    background-image: -webkit-linear-gradient(left, #3498db, #f47920 10%, #d71345 20%, #f7acbc 30%, #ffd400 40%, #3498db 50%, #f47920 60%, #d71345 70%, #f7acbc 80%, #ffd400 90%, #3498db);
    color: transparent;
    -webkit-background-clip: text;
    background-size: 200% 100%;
    animation: slide 5s infinite linear;
    font-size: 40px;
  }

  .test {
    height: 200px;
  }

  .model {
    position: fixed;
    z-index: 10;
    width: 100vw;
    height: 100vh;
    overflow: auto;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.8);
  }

  .model-show {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100vw;
    height: 100vh;
  }

  .model img {
    display: block;
    margin: auto;
    max-width: 80%;
    user-select: none;
    background-position: 0px 0px, 10px 10px;
    background-size: 20px 20px;
    background-image: linear-gradient(45deg, #eee 25%, transparent 25%, transparent 75%, #eee 75%, #eee 100%), linear-gradient(45deg, #eee 25%, white 25%, white 75%, #eee 75%, #eee 100%);
  }

  .c-item {
    display: block;
    padding: 10px 0;
    user-select: none;
  }

  @keyframes slide {
    0% {
      background-position: 0 0;
    }

    100% {
      background-position: -100% 0;
    }
  }

  @media screen and (max-width: 1000px) {
    .content {
      max-width: 90%;
      margin: auto;
    }

    .test {
      height: 400px;
    }
  }
</style>
