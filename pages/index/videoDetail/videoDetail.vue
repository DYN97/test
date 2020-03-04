 
<template>
	<view>
		<view class="uni-common-mt">
			<view> 
				<video :style="{'width':'750rpx'}" id="myVideo" :src="moviesrc" @error="videoErrorCallback" controls></video>
				<view :style="{'height':titleHeight}">
					<view class="uni-label">
						<view class="uni-flex uni-row">
							<view class="flex-item">
								<image @click="tapLove" :src="checked?'../../../static/aixinRed.png':'../../../static/aixin.png'" class="img"></image><text
								 class="numtext">{{praise}}</text>
							</view>
							<view class="flex-item">
								<image src="../../../static/xiaoxi.png" class="img" @click="OpenCommentBox('')"></image><text class="numtext">{{comments}}</text>
							</view>
						</view>				
					</view>
					
				</view>
				<view class="uni-label" :style="{'height':titleHeight,'font-size':'18px','padding-left':'20upx'}">评论列表</view>
			</view>
			
			<!-- #ifndef MP-ALIPAY -->
			<scroll-view scroll-y="true" :style="{'height':videoHeight}">
				<view class="uni-list uni-common-mt">
					<view class="uni-list-cell">

						<view class="uni-list-cell-db">
							<view class="uni-padding-wrap">
								<!-- 评论区 start -->
								<view class="uni-comment" v-if="CommentList.length>0">
									<view class="uni-comment-list" v-for="(item,i) in CommentList" :key='"father"+i'>
										<view class="uni-comment-face">
											<image :src="item.headimg" mode="widthFix"></image>
										</view>
										<view class="uni-comment-body">
											<view class="uni-comment-top">
												<text>{{item.nickname}}</text>
											</view>
											<view class="uni-comment-content">{{item.comment}}</view>
											<view class="uni-comment-date">
												<image v-if="!item.self" src="../../../static/xiaoxi.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
												 @click="OpenCommentBox('','',item.id,item.nickname)"></image>
												<image v-if="item.self" src="../../../static/edit.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
												 @click="OpenCommentBox(item.comment,item.id)"></image>
												<image v-if="item.self" src="../../../static/cancel.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
												 @click="showCancel(item.id)"></image>
												<text>{{item.comment_date}}</text>
											</view>
											
											<view class="uni-comment-child-list" v-if="item.childlist.length>0" >
												<em></em>
												<span></span>
												
												<view class="uni-comment-child-body" v-for="(childitem,k) in item.childlist" :key='"child"+k'>
													<view class="uni-comment-child-top">
														<text>{{childitem.nickname}}</text><text style="color: #808080;">回复</text><text>{{childitem.tofullname}}</text>
													</view>
													<view class="uni-comment-child-content">{{childitem.comment}}</view>
													<view class="uni-comment-child-date">
														<image v-if="!childitem.self" src="../../../static/xiaoxi.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
														 @click="OpenCommentBox('',childitem.id,item.id,childitem.nickname,childitem.fromuser)"></image>
														<image v-if="childitem.self" src="../../../static/edit.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
														 @click="OpenCommentBox(childitem.comment,childitem.id)"></image>
														<image v-if="childitem.self" src="../../../static/cancel.png" style="width: 40upx;height: 40upx;margin-right: 20upx;"
														 @click="showCancel(childitem.id)"></image>
														<text>{{childitem.comment_date}}</text>
													</view>
													<label v-if="k!=item.childlist.length-1"></label>
												</view>

											</view>

										</view>
										
									</view>
								</view>
								<view v-if="CommentList.length==0" style="text-align: center;">
									<text>暂无评论</text>
								</view>
							</view>
						</view>
					</view>
				</view>
				<!-- #endif -->
			</scroll-view>
		</view>
		<neil-modal :show="modalshow" title="评论" @cancel="close(0)" @confirm="pushComment" @close="close(1)">
			<view class="input-view">
				<view class="input-name">
					<textarea :focus='true' v-model="comment" placeholder-style="color:#000000" placeholder="写写你的看法.." :style="{height:`${0.2*this.sysheight}px`,'background-color':`#ffffff`,padding:`30upx`,'border-radius':`15upx`}" />
					</view>
		    </view>
		</neil-modal>
		<neil-modal 
		    :show="cancelshow" 
		    @close="cancelshow=false" 
			align="center"
		    title="撤销评论" 
		    content="是否撤销当前评论"
		    @cancel="cancelshow=false" 
		    @confirm="CancelComment">
		</neil-modal>
		<!-- <uni-popup ref="popup" type="center" :maskClick='false'>
			<view> 
				<textarea :focus='true'
				v-model="comment"
				placeholder-style="color:#000000"
				 placeholder="写写你的看法.." 
				 :style="{height:`${0.2*this.sysheight}px`,'background-color':`#ffffff`,padding:`30upx`,'border-radius':`15upx`}" />
				
			</view>
			<view style="margin-top: 30upx;text-align: right;">
				<button type='primary' style="margin-right: 30upx;" size="mini" @click="pushComment">提交</button>
				<button type='default' size="mini" @click="close">返回</button></view>
		</uni-popup> -->
	</view>
	
</template>

<script>
	import uniList from "@/components/uni-list/uni-list.vue"
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	import neilModal from '@/components/neil-modal/neil-modal.vue';
	export default {
		name: 'HelloWorld',
		components: {
			uniList,
			uniListItem,
			neilModal,
			uniPopup
		},
		onLoad(option){
			this.movieid = option.id;
		},
		data() {
			return {
				sysheight:0,
				height:"",
				width:"",
				praise:"0",
				comment:"",
				comments:"0",
				modalshow: false,
				listHeight:0,
				rastHeight:0,
				titleHeight:0,
				movieid:"",
				moviesrc:"",
				canComment:true,
				cancelshow:false,
				checked:true,
				commentway:0,
				chosedcommentid:"",
				commenttouser:'',
				commentfromuser:'',
				islogin:false,
				CommentList: []
			}
		},
		mounted() {
			var me = this;
			me.GetMovieDetail()
			this.initVideo();
		},
		created() {
			let systeminfo = uni.getSystemInfoSync();
			console.log(systeminfo);
			this.sysheight = uni.getSystemInfoSync().safeArea.height;
			this.height = `${this.sysheight}px` ;
			let width = uni.getSystemInfoSync().windowWidth ;
			this.width = `${width}px` ;
			
			this.rastHeight= this.sysheight-235;
			this.titleHeight= `${0.1*this.rastHeight}px`;
			this.videoHeight= `${0.8*this.rastHeight}px`;
			console.log(this.rastHeight,this.titleHeight,this.videoHeight)
		},
		methods: {
			initVideo() {
				 this.videoContext = uni.createVideoContext('myVideo')
				//初始化视频方法 循环列表获取每个视频的id
				
					// let myPlayer = this.$video('myVideo', {
					// 	//确定播放器是否具有用户可以与之交互的控件。没有控件，启动视频播放的唯一方法是使用autoplay属性或通过Player API。
					// 	controls: true,
					// 	//自动播放属性,muted:静音播放
					// 	autoplay: "muted",
					// 	//建议浏览器是否应在<video>加载元素后立即开始下载视频数据。
					// 	//preload: "auto",
					// 	//设置视频播放器的显示宽度（以像素为单位）
					// 	width: "800px",
					// 	//设置视频播放器的显示高度（以像素为单位）
					// 	height: "400px"
					// });
			
			},
			OpenCommentBox(comment,id,fatherid,fathername,fromuser){
				if(!this.islogin){
					alert("请先登录！");
					var url = "/mobile/page/movie.aspx?movieid="+this.movieid;
					location.href = url;
					return false;					
				}
				if(comment){
					this.comment = comment;
					this.commentway = 1;
					this.chosedcommentid = id;
				}
				if(fatherid)
				{
					this.commentway = 0;
					this.chosedcommentid = fatherid;
					this.commenttouser = fathername;	
					this.commentfromuser = fromuser;	
				}
				this.modalshow = true;
				
			},
			showCancel(id){
				this.chosedcommentid = id;
				this.cancelshow = true;
			},
			close(type){
				if(type==0){
					this.modalshow =false;
					this.comment = "";
				}else{
					this.modalshow =false;
				}
			},
			CancelComment(){
				var me = this;
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: '/service/movie/EditComment.aspx?commentid='+me.chosedcommentid+'&type=-1',
						success: (res) => {			
							alert("撤销评论成功！");
							me.GetMovieDetail();
							me.cancelshow =false;
							me.comment = "";
							
							
							resolve()
						}
					})
				}) 
				return promise
				
			},
			GetMovieDetail(){
				var me = this;
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: '/service/movie/GetMovieDetail.aspx?movieid='+me.movieid,
						success: (res) => {						
							console.log(res);
							var item = res.data.data;
							me.moviesrc = item.url;
							console.log(item.url);
							me.checked = item.checked=="1"?true:false;
							me.poster = item.coverimage;
							me.praise = item.praise;
							me.comments = item.comments;
							if(item.commentlist&&item.commentlist.length>0){
								me.CommentList = item.commentlist;
							}
							me.islogin = item.islogin=="1"?true:false;
							resolve()
						}
					})
				}) 
				return promise
			},
			tapLove(){
				if(!this.islogin){
					alert("请先登录！");
					var url = "/mobile/page/movie.aspx?movieid="+this.movieid;
					location.href = url;
					return false;					
				}
				this.checked = !this.checked;
				
				this.praise = this.checked?parseFloat(this.praise)+1:parseFloat(this.praise)-1;
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: '/service/movie/MoviePraise.aspx?movieid='+this.movieid+'&status='+(this.checked?1:0),
						success: (res) => {
							resolve()
						}
					})
				}) 
				
			},
			pushComment(){
				if(!this.canComment){
					return false;
				} 
				if(this.comment==""){
					alert("请填写评论内容！");
					this.canComment = true;
					this.modalshow =false;
					return false;
				}
				this.canComment = false;
				var me = this;
				let promise = new Promise((resolve,reject)=>{
					if(me.commentway==0){
						uni.request({
							url: '/service/movie/MovieComment.aspx',
							data:{
								movieid:me.movieid,
								comment:me.comment,
								tousername:me.commenttouser,
								touser:me.commentfromuser,
								fatherid:me.chosedcommentid
								
							},
							success: (res) => {
								console.log(res);
								me.canComment = true;
								me.comment = "";
								alert("评论成功！");
								me.GetMovieDetail();
								me.modalshow =false;
								me.chosedcommentid ="";
								me.commenttouser ="";							
								
								resolve()
							}
						})
					}else{
						uni.request({
							url: '/service/movie/EditComment.aspx',
							data:{
								commentid:me.chosedcommentid,
								comment:me.comment,
								type:1
							},
							success: (res) => {
								console.log(res);
								me.canComment = true;
								me.comment = "";
								alert("评论成功！");
								me.GetMovieDetail();
								me.modalshow =false;
								me.chosedcommentid ="";
								me.comment = "";
								resolve()
							}
						})
					}
					
				}) 
				
				
			},
			videoErrorCallback(){
            uni.showModal({
                content: e.target.errMsg,
                showCancel: false
            })
        },
		}
	}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	/* comment */
	page {
		background-color: #f8f8f8;
	}
	.uni-padding-wrap {
		padding: 30upx;
	}
	view {
		font-size: 28upx;
	}
	.uni-comment {
		display: flex;
		flex-grow: 1;
		flex-direction: column;
	}
	.uni-comment-list {
		flex-wrap: nowrap;
		padding: 10rpx 0;
		margin: 10rpx 0;
		width: 100%;
		display: flex;
	}
	.numtext{
		height: 30px;
    display: inline-block;
    position: absolute;
    line-height: 30px;
	}
	
	.uni-comment-child-list em{
		display:block; 
	border-width:10px; position:absolute; top:-20px; left:20upx;border-style:solid dashed dashed; border-color: transparent transparent #09F;font-size:0; line-height:0;}
	.uni-comment-child-list span{display:block; border-width:10px; position:absolute; top:-19px; left:20upx;border-style:solid dashed dashed; border-color: transparent transparent #FFF;font-size:0; line-height:0;}
	
	.uni-comment-child-list label
	{
		display: block;
		width: 90%;
		    /* height: 1px; */
		margin: 0 auto;
		border-bottom: 1px #ccc dashed;
	}
	
	.uni-comment-child-list {
		flex-wrap: wrap;
		padding: 10rpx;
		margin: 10rpx 0;
		width: 100%;
		display: flex;
		 border:1px solid #09F; position:relative; background-color:#FFF;
	}
	.uni-comment-face {
		width: 70upx;
		height: 70upx;
		border-radius: 100%;
		margin-right: 20upx;
		flex-shrink: 0;
		overflow: hidden;
	}
	.uni-comment-face image {
		width: 100%;
		border-radius: 100%;
	}
	.uni-comment-body {
		width: 100%;
		
	}
	.uni-comment-top {
		line-height: 1.5em;
		justify-content: space-between;
	}
	.uni-comment-top text {
		color: #0A98D5;
		font-size: 24upx;
	}
	.uni-comment-date {
		line-height: 38upx;
		flex-direction: row;
		justify-content: flex-end;
		display: flex !important;
		flex-grow: 1;
	}
	.uni-comment-date view {
		color: #666666;
		font-size: 24upx;
		line-height: 38upx;
	}
	.uni-comment-content {
		line-height: 1.6em;
		font-size: 28upx;
		padding: 8rpx 0;
	}
.uni-comment-child-face {
		width: 70upx;
		height: 70upx;
		border-radius: 100%;
		margin-right: 20upx;
		flex-shrink: 0;
		overflow: hidden;
	}
	.uni-comment-child-face image {
		width: 100%;
		border-radius: 100%;
	}
	.uni-comment-child-body {
		width: 100%;
		/* border-bottom: #808080 dashed 1px; */
	}
	.uni-comment-child-top {
		line-height: 1.5em;
		justify-content: space-between;
	}
	.uni-comment-child-top text {
		color: #0A98D5;
		font-size: 24upx;
	}
	.uni-comment-child-date {
		line-height: 38upx;
		flex-direction: row;
		justify-content: flex-end;
		display: flex !important;
		flex-grow: 1;
	}
	.uni-comment-child-date view {
		color: #666666;
		font-size: 24upx;
		line-height: 38upx;
	}
	.uni-comment-child-content {
		line-height: 1.6em;
		font-size: 28upx;
		padding: 8rpx 0;
	}
	.uni-comment-replay-btn {
		background: #FFF;
		font-size: 24upx;
		line-height: 28upx;
		padding: 5rpx 20upx;
		border-radius: 30upx;
		color: #333 !important;
		margin: 0 10upx;
	}
	.img{
		height: 60upx;
		width: 60upx;
		
		opacity: 0.9;
	}
	.right-text{
		text-align: center;
		font-size: 14px;
		margin-bottom: 50upx;
		height: 20px;
	}
	.uni-flex {
    display: -webkit-box;
    display: -webkit-flex;
    display: flex;
    -webkit-box-orient: horizontal;
    -webkit-box-direction: normal;
    -webkit-flex-direction: row;
    flex-direction: row;
	justify-content:  flex-end ;
}
.flex-item{
	
	    width: 20%;
	    
	    text-align: center;
}
</style>

