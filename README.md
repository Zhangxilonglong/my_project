## vue-demo—后台管理系统
### 练习使用vue-router的小demo
### 1 安装和配置路由
#### 1.1 安装
	npm install vue-router@3.5.2 -S
#### 1.2 配置
	// 在router文件夹下的index.js文件中进行配置
	import Vue form 'vue'
	import VueRouter from 'vue-router'
	// 在vue中安装插件
	Vue.use(VueRouter)
	// 实例化
	const router = new  VueRouter()
	// 共享router
	export default  router
#### 1.3 在main.js中导入并挂载
	// 导入路由模块
	import router from '@/components/router/index.js'
	//挂载
	new  Vue({
		render:  h  =>  h(App)，
		router:router
	}).$mount('#app')
### 2 基于路由渲染登录组件
#### 2.1 index.js中进行配置
	// 导入需要的登录组件
	import Login from '@/components/MyLogin.vue'
	// 登录页面的路由规则
	{path:'/',redirect:'/login'},
	{path:'/login',component:Login}
#### 2.2 APP.vue中路由占位符
	<router-view></router-view>
### 3 模拟登录功能
#### 3.1 账户名 密码的数据双向绑定
	<input v-model.trim="username">
	<input v-model.trim="psssword">
#### 3.2 为重置 登录按钮绑定点击事件
	<button @click="reset">重置</button>
	<button @click="login">登录</button>
	————————————————————————————————————————————————————————————————————
	// 定义事件函数
	reset(){
		this.username='',
		this.password=''
		},
	login(){
		if(this.username==='admin'&&this.password==='666666'){
			// 登录成功
			localStorage.setItem('token','Bearer xxxx')
			// 跳转到后台主页
			this.$router.push('/home')
		}else{
		// 登录失败
			localStorage.removeItem('token')
			alert('密码或账户名错误')
		}
	}
### 4 后台首页myhome的布局
#### 4.1 定义后台主页的路由规则
#### 4.2 注册 导入各组件
	// 导入并注册
	import MyHeader from './subcomponents/MyHeader.vue'
	--------------------------------------------------------------------
	export default {
		name: 'MyHome',
		// 注册组件
		components: {
			MyHeader,
			MyAside,
		},
	}
#### 4.3 使用各个组件
	<template>
		<div  class="home-container">
			<!-- 头部区域 -->
			<MyHeader></MyHeader>
			<!-- 页面主体区域 -->
			<div  class="home-main-box">
				<!-- 左侧边栏 -->
				<MyAside></MyAside>
				<!-- 右侧内容主体区域 -->
				<div  class="home-main-body">123</div>
			</div>
		</div>
	</template>
#### 4.4 header 退出登录并控制访问权限
	// 退出登录按钮绑定事件
	<button@click="logout">退出登录</button>
	// 定义事件
	logout(){
		//1、清空token
		localStorage.removeItem('Token')
		//2、跳转到登录页面重新登录
		this.$router.push('/login')
	}
	// 路由前置守卫
	router.beforeEach(function (to,from,next) {
		if(to.path==='/home'){
			if(localStorage.getItem('token')){
				next()
			}else{
				next('/login')
			}
		}else{
			next()
		}
	})
#### 4.5 header 点击侧边栏实现子路由的嵌套展示
	<template>
		<div  class="layout-aside-container">
			<!-- 左侧边栏列表 -->
			<ul  class="user-select-none menu">
				<li  class="menu-item"><router-link  to="/home/users">用户管理</router-link></li>
				<li  class="menu-item"><router-link  to="/home/rightmanage">权限管理</router-link></li>
				<li  class="menu-item"><router-link  to="/home/goodsmanage">商品管理</router-link></li>
				<li  class="menu-item"><router-link  to="/home/ordersmanage">订单管理</router-link></li>
				<li  class="menu-item"><router-link  to="/home/settings">系统设置</router-link></li>
			</ul>
		</div>
	</template>
	
	// home子路由规则：
	{
		path:'/home',
		component:Home,
		redirect:'/home/users',
		children:[
			{path:'users',component:Users},
			{path:'rightmanage',component:Rights},
			{path:'goodsmanage',component:Goods},
			{path:'ordersmanage',component:Orders},
			{path:'settings',component:Setting},
			//用户详情页的路由规则
			{path:'userinfo/:id',component:MyUserDetail,props:true},
		]
	}
#### 4.6用户管理——用户列表展示
	// 使用v-for方法，依次将用户数据渲染到表格上
	<tbody>
		<tr  v-for="item  in  userlist" :key=item.id>
			<td>{{item.id}}</td>
			<td>{{item.name}}</td>
			<td>{{item.age}}</td>
			<td>{{item.position}}</td>
			<td><a  href="#" @click.prevent="gotoDetail(item.id)">详情 </a></td>
		</tr>
	</tbody>
#### 其他功能的优化
