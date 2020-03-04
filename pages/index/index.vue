<template>
	<view>
		<uni-list>
		    <uni-list-item :title="item.name" 
			:note="item.content" 
			:show-arrow="false" 
			v-for="item in movieList" 
			:thumb="item.coverimagesrc"
			:key='item.id' 
			@click='gotodetail(item.id)'></uni-list-item>
		   
		</uni-list>
	</view>
</template>

<script>
	import uniList from "@/components/uni-list/uni-list.vue"
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	export default {
		name: 'HelloWorld',
		components: {
			uniList,
			uniListItem
		},
		data() {
			return {
				movieList:[
				]
			}
		},
		mounted() {
			this.pushVideoList();
		},
		methods: {
			gotodetail(id){
				uni.redirectTo({
				    url: './videoDetail/videoDetail?id='+id
				});
			},pushVideoList(){
				var me = this;
				let promise = new Promise((resolve,reject)=>{
					uni.request({
						url: 'http://www.aishow.online/service/movie/getmovielist.aspx',
						success: (res) => {
							let videoGroup = []
							for (let item of res.data.data) {
									videoGroup.push({
										src:item.url,
										id:item.id,
										content:item.content,
										coverimagesrc:item.coverimage,
										name:item.name,
										like:item.praise,
										comment:item.comments,
										initialTime:0,
										duration:item.duration,
										coverimageshow:true,
										resolving:item.resolving
									})
								
							}
							this.movieList = [...this.movieList,...videoGroup];
							resolve()
						}
					})
				}) 
				return promise
			},
		}
	}
</script>

<style>

</style>
