<template>
	<div class="contain" ref="dragContain">
		<div class="control" v-drag="contain">
			<div style="line-height:30px;background:#409EFF;cursor:move;color:#fff;text-indent:15px;">
				控制面板
			</div>
			<div style="padding:7px;" @mousedown="mouseDown($event,false)">
				<el-form ref="form" size="mini" label-width="70px">
				  <el-form-item label="模板名称">
				    <el-input v-model="name"></el-input>
				  </el-form-item>
				  <el-row  v-if="json.length">
					  <el-form-item>
					    <el-button type="primary" @click="onSave">保存</el-button>
					  </el-form-item>
					  <el-form-item label="宽度">
					    <el-input v-model.lazy="json[divindex].style.width"></el-input>
					  </el-form-item>
					  <el-form-item label="高度">
					    <el-input v-model.lazy="json[divindex].style.height"></el-input>
					  </el-form-item>
					  <el-form-item label="字体大小">
					    <el-select v-model.lazy="json[divindex].style.fontSize" placeholder="请选择字体大小">
					      <el-option label="12px" value="12px"></el-option>
					      <el-option label="14px" value="14px"></el-option>
					      <el-option label="16px" value="16px"></el-option>
					      <el-option label="18px" value="18px"></el-option>
					    </el-select>
					  </el-form-item>
					  <el-row>
						  <el-col :span="12">
						  	<el-form-item label="背景颜色">
						  	<el-color-picker v-model.lazy="json[divindex].style.backgroundColor" show-alpha></el-color-picker>
						  	</el-form-item>
						  </el-col>
						  <el-col :span="12">
						  	<el-form-item label="文字颜色">
						  	<el-color-picker v-model.lazy="json[divindex].style.color"></el-color-picker>
						  	</el-form-item>
						  </el-col>
					  </el-row>
					    
					  <el-form-item label="文字内容">
					    <el-input type="textarea" v-model="json[divindex].text"></el-input>
					  </el-form-item>
				  </el-row>
				    <el-row>
					  <el-button type="primary" size="mini" @click="addDive()">新增div</el-button>
					</el-row>
				</el-form>
			</div>
		</div>
		
     	<div v-drag="contain" class="drag" :class="{active:index == divindex}" v-if="json.length" v-for="(item,index) in json" @click="moveDiv(index,$event)" :style="item.style">
     		{{item.text}}
     		<i class="el-icon-menu sacle" v-if="index == divindex" @click="sacle($event)" @mousedown="mouseDown($event,true)"  unselectable="on"></i>
     		<el-button type="danger" class="del"  @click="del(index,$event)" v-if="index == divindex" icon="el-icon-delete" circle></el-button>
     	</div>
     	<div v-if="!json.length">暂时没有内容哦！！期待您的编辑</div>
	</div>
</template>
<script >
// import API from '@/api/api_user';
export default {
	name:'vue-drag-infinite',
	props: {
        json: {
            type: Array,
            default: []
        },
        name: {
            type: String,
            default: '小清新'
        },
    },
	data(){
		return {
			//外层容器的宽高 数值不带px单位
			contain:{
				xx:500,
				yy:500
			},
			//存放拉伸放大时的  div的原始宽高 带px单位
			movejson:{
				moveWidth:'',
				moveHeight:''
			},
			divindex:0,//当前选中div的序号  默认为0
		}
	},
	mounted(){  
	    this.contain.xx = this.$refs.dragContain.offsetWidth;
	    this.contain.yy = this.$refs.dragContain.offsetHeight;
	},
	methods:{
		// 字符串转json对象
		toJson(str){
          let _str =eval('(' + str + ')');
          return _str;
        },
        //外层div点击选择事件 绑定右上角设置面板
        moveDiv(index,e){
        	let jsonNew = this.toJson('{"'+e.target.style.cssText.replace(/\; +/g, "\",\"").replace(/\: +/g, "\":\"").replace(/\;+/g, "").replace(/-\w/g, function ($) {return $.slice(1).toUpperCase();})+'"}');
        	//当前选中div的序号
        	this.divindex = index;
        	
        	// 设置div框的 各种属性
        	for (let em in jsonNew) {
			    this.json[index].style[em]= jsonNew[em]; 
			}
        	this.json[index].text= e.target.innerText;
       
        },
        //添加一个div块 默认的
        addDive(){
        	let newJson = {
				style:{
					width:"80px",
					height:"50px",
					fontSize:"14px",
					color:"#ffffff",
					backgroundColor:"#333333"
				},
				text:'新的div'
			};
			this.json.push(newJson);
        },
        //删除当前div
        del(i,e){
        	this.json.splice(i, 1);
        	// console.log(i,this.json.length)
        	if(i == this.json.length){
        		this.divindex = 0;
        	}
        	e.stopPropagation();
        },
        onSave(){
        	// console.log(this.json,'11')
        	let data = this.json;
        	this.$emit('save',data)
        	// let diyJson = {
        	// 	diyName:this.diyName,
        	// 	diyJson:JSON.stringify(this.json)
        	// };
        	// if(this.$route.query.id){
        	// 	diyJson.diyId = this.$route.query.id;
        	// }
        	// API.addDiy(diyJson).then((res)=> {
	        // 	if(res.status == '0'){
	        // 		this.$message({
		       //        message: res.result,
		       //        type: 'success'
		       //      });
	        // 		this.$router.push({path:'/user/diyList'})
	        // 	}else{
	        // 		this.$message({
		       //        message: res.msg,
		       //        type: 'warning'
		       //      });
	        // 	}
	        // });
        },
        //div上拉伸的阻止冒泡事件
        sacle(e){
        	e.stopPropagation();
        },
        //div上拉伸的 放大 缩小div宽高
        mouseDown(e,type){
        	if(type){
	        	let that = this;
	        	let oldX = e.pageX,oldY = e.pageY;
	        	that.movejson.moveWidth = that.json[that.divindex].style.width;
	        	that.movejson.moveHeight = that.json[that.divindex].style.height;
	        	document.onmousemove = function (e){
	        		let newX = e.pageX - oldX;
	        		let newY = e.pageY - oldY;
	        		that.json[that.divindex].style.width = ((newX+ parseInt(that.movejson.moveWidth))>=80?(newX+ parseInt(that.movejson.moveWidth)):'80')+'px';
	        		that.json[that.divindex].style.height = ((newY+ parseInt(that.movejson.moveHeight))>=50?(newY+ parseInt(that.movejson.moveHeight)):'50')+'px';
	        	}
	        	document.onmouseup = function(){
	                document.onmousemove = document.onmouseup = null;
	            }
            }
        	e.stopPropagation();
        }

      
	},
	computed: {

	},
	directives:{
		drag:{
            bind: function (el, binding, vnode) {
              el.onmousedown = function(e){
                var disx = e.clientX - el.offsetLeft;
                var disy = e.clientY - el.offsetTop;
                document.onmousemove = function (e){
                	let left = e.clientX - disx;
                	let top = e.clientY - disy;
                	if(left<0){
                    	left = 0;
                    }
                    if(top<0){
                    	top = 0;
                    }
                    if(left>binding.value.xx-el.offsetWidth){
                    	left = binding.value.xx-el.offsetWidth;
                    }
                    if(top>binding.value.yy-el.offsetHeight){
                    	top = binding.value.yy-el.offsetHeight;
                    }
                    el.style.left = left+'px';
                    el.style.top = top+'px';
                }
                document.onmouseup = function(){
                    document.onmousemove = document.onmouseup = null;
                }
            }

          },
        
        }
    }

}

</script>
<style lang='scss' scoped>
	ul,li{margin:0px;list-style:none;padding:0px;}
	.contain{height: 700px;background:#ddd; position: relative;user-select:none;}
	.el-form-item--mini.el-form-item{
		margin-bottom:7px;
	}
	.drag{
            width: 100px;
            height: 100px;
            position: absolute;
            top: 0;
            left: 0;
            cursor:move;
            background-color: red;
            border:1px solid #FFF;
            line-height:1.7;
            box-sizing:border-box;
            color:#fff;
            overflow: hidden;
            padding:5px;
            .sacle{
				position:absolute;
				right:0px;
				bottom:0px;
				background-color:#909399;
				cursor: se-resize;
				font-size:15px;
			}
			.del{
				position:absolute;
				top:0px;
				right:0px;
				padding:5px;
				box-shadow:0px 0px 10px rgba(255, 255, 255,0.5);
			}
	}
	.drag.active{
		border-radius:5px;
		border:1px solid #85ce61;
		box-shadow:0px 0px 20px rgba(0,0,0,0.5);
		
	}
	.control{
		width:250px;
		border-radius:5px;
		overflow:hidden;
		background-color:#B3C0D1;
		position:absolute;
		z-index:999;
		right:20px;
		top:15px;
	}

</style>