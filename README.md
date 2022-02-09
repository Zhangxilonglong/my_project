### my_project
Some web front end projects
## vue-demo—程序员头条
#### 移动端程序员头条，练习使用vue的小demo，包含vue-router，使用EsLint约束代码格式，Vant组件库使用；基本功能包括展示文章、下拉刷新、上拉加载更多、‘我的’账户信息等
### Vant使用手册 https://vant-contrib.gitee.io/vant/#/zh-CN
### 1 创建移动端头条vue项目
#### 1.1 创建vue项目
	vue create my-toutiao-demo
	// vue项目名中不能包含大写字母，否则无法创建
##### 勾选配置选项  Choose Vue Version、Babel、Router等
![创建vue项目](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/create-vue.png)  
##### PS：项目文件夹中：views文件夹存放通过路由切换的组件，components文件存放其他可复用组件
### 2 配置路由
#### 2.1 配置
	// 在router文件夹下的index.js文件中进行配置
	import Vue form 'vue'
	import VueRouter from 'vue-router'
	// 在vue中安装插件
	Vue.use(VueRouter)
	// 路由规则数组
	const  routes = []
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
### 2 安装并配置Vant组件库
#### 2.1 安装
	npm i vant@2 -S
#### 2.2 导入组件
	//导入并安装组件   一次性导入了全部组件
	import Vant from 'vant'
	import 'vant/lib/index.css'
	Vue.use(Vant)
### 3 底部Tabber 首页 我的
#### 使用Tabber组件并开启路由
![Tabber](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/Home_My.png)
	
### 4 顶部导航栏
#### 使用Navbar组件
	
	<!-- 头部区域 -->
	<van-nav-bar  title="程序员头条"  fixed/>
	<!-- van-nav-bar的属性fixed 默认值为false 此处改为true 实现导航栏固定 -->
	
### 5 Home-文章列表展示
##### 从指定接口中请求文章列表数据，安装axios
#### 5.1 axios安装与配置
	// 在项目根目录中运行命令
	npm install axios -S
	// vue项目中配置axios
	1.方案1  把axios挂载到vue的prototype上，main.js照片中导入axios
		     Vue.prototype.$http=axios，该方案可复用性差，不推荐
	2.方案2  封装utils目录下的request模块，复用性较好
![axios](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/request.png)
#### 5.2 获取文章列表数据
##### 在Home组件中封装获取文章列表的数据，在created生命周期函数中调用该方法，实现刚打开页面时就获取数据进行展示。
![获取文章列表数据](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/initArticleList.png)
##### 首页有多个页面需要获取数据，因此上述代码会被用到多次，为提高效率，将上述代码封装成articleAPI模块
![封装articleAPI](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/ArticleAPI.png)
##### 封装之后使用ArticleAPI
	methods: {
		async  initArticleList() {
		const { data: res } = await  getArticleListAPI(this.page, this.limit)
		}
	},
	created() {
		this.initArticleList()
	}
#### 5.3 展示文章列表
##### 5.3.1 文章信息（作者、评论数...）
##### 导入  注册   使用ArticleInfo组件
	// 导入
	import  ArticleInfo  from  '@/components/Article/ArticleInfo.vue'
	
	//注册
	components: {
		ArticleInfo
	}
	
	// 使用 
	// 为ArticleInfo组件封装props属性
	props: {
		title: {
			type:  String,
			default:  ''
		},
		author: {...},
		cmtCount: {...},
		time: {...}
	}
	--------------------------------------------------------------
	<!-- 循环渲染ArticleInfo组件 使用时传入文章的各项信息 -->
	<ArticleInfo  v-for="item  in  articlelist"
		:key="item.id"
		:title=item.title
		:author=item.aut_name
		:cmtCount=item.comm_count
		:time=item.pubdate>
	</ArticleInfo>
##### 5.3.2 文章封面cover
##### 为ArticleInfo组件封装cover属性；
##### 文章封面有3种类型，无图片、一张图片和三张图片，因此在渲染文章封面时要用v-if判断封面的类型
	<!-- 单张图片 -->
	<img :src="cover.images[0]"  alt=""  class="thumb"  v-if="cover.type===1">
	<!-- 三张图片 -->
	<div  class="thumb-box"  v-if="cover.type===3">
		<img :src="item"  alt=""  class="thumb"  v-for="(item,i) in  cover.images" :key=i>
	</div>
### 6 Home-文章列表上拉 下拉
#### 6.1 上拉加载更多
##### List列表：瀑布流滚动加载，用于展示长列表，当列表即将滚动到底部时，会触发事件并加载更多列表项。
	//将需要展示的所有ArticleInfo组件放到<van-list>标签中
	<van-list  v-model="loading" :finished="finished"  finished-text="我是有底线的" @load="onLoad">
	   ...
	</van-list>
##### List 组件通过 `loading` 和 `finished` 两个变量控制加载状态，当组件滚动到底部时，会触发 `load` 事件并将 `loading` 设置成 `true`。此时可以发起异步操作并更新数据，数据更新完毕后，将 `loading` 设置成 `false` ；若数据已全部加载完毕，则直接将 `finished` 设置成 `true` 。
	//定义onLoad()方法：组件滚动到底部时，触发load事件，执行onLoad函数，请求下一页数据
	onLoad(){
		// 让页码值+1
		this.page++
		// 重新请求接口获取数据
		this.initArticleList()
	}
	// 需要注意的是：每次请求新数据之后，要将新数据添加在旧数据数组之后，
	   并且判断接口中的数据是否全部请求完成，如果请求完成，finished设置为true
#### 6.2下拉刷新
##### 将上述已经实现下拉加载更多的代码块，放在<van-pull-refresh>标签中，实现下拉刷新
	<van-pull-refresh  v-model="isloading" :disabled="finished" @refresh="onRefresh">
		<van-list  v-model="loading" :finished="finished"  finished-text="我是有底线的" @load="onLoad">
			<ArticleInfo  v-for="item  in  articlelist"
			...
			</ArticleInfo>
		</van-list>
	</van-pull-refresh>
	// 需要注意的是，每次加载数据时，要判断是上拉加载数据还是下拉刷新数据：
	   上拉加载数据要将新数据数组拼接在旧数据数组的后面；
	   下拉刷新数据要将新数据数组拼接在旧数据数组的前面。
### 至此，移动端程序员头条的基本功能以实现，后续学习中会继续完善，添加点击文章，用户登录等功能。
![移动端程序员头条最终效果](https://github.com/Zhangxilonglong/my_project/raw/vue-demo-移动端程序员头条/images/toutiao1.png)
