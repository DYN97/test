<template>
	<view>
		<!-- 一般用法 -->
		<uni-list>
		    <uni-list-item title="标题文字" :show-arrow="false"></uni-list-item>
		    <uni-list-item title="标题文字"></uni-list-item>
		    <uni-list-item title="标题文字" :show-badge="true" badge-text="12"></uni-list-item>
		    <uni-list-item title="禁用状态" :disabled="true" :show-badge="true" badge-text="12"></uni-list-item>
		</uni-list>
		
		<!-- 带描述信息 -->
		<uni-list>
		    <uni-list-item title="标题文字" note="描述信息"></uni-list-item>
		    <uni-list-item title="标题文字" note="描述信息" :show-badge="true" badge-text="12"></uni-list-item>
		</uni-list>
		
		<!-- 包含图片 -->
		<uni-list>
		    <uni-list-item title="标题文字" thumb="https://img-cdn-qiniu.dcloud.net.cn/new-page/hx.png"></uni-list-item>
		</uni-list>
		
		<!-- 包含图标 -->
		<uni-list>
		    <uni-list-item title="标题文字" :show-extra-icon="true" :extra-icon="{color: '#4cd964',size: '22',type: 'spinner'}"></uni-list-item>
		</uni-list>
		
		<!-- 显示Switch -->
		<uni-list>
		    <uni-list-item title="标题文字" :show-switch="true" :show-arrow="false"></uni-list-item>
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
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
