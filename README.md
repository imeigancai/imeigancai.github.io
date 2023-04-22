<!DOCTYPE HTML>
<html>
	<head>
		<title>Untitled</title>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
		<link rel="stylesheet" href="assets/css/main.css" />
		<noscript><link rel="stylesheet" href="assets/css/noscript.css" /></noscript>
		<script src="js/vue.min.js" language="JavaScript"></script>
		<script src="js/jquery-3.4.1.min.js"></script>
		<script src="js/html2canvas.min.js"></script>
		<style>
			.mountPart{
				text-align: center;
				background-color: #313131;
				height: 80%;
				border-radius: 1.6em;
				letter-spacing: 0.5em;
				line-height: 2em;
				padding: 1em;
				height: 6.5em;
				opacity: 0.9;
			}
		</style>
	</head>
	<body class="is-preload" onload="loaded()">
		
		<!-- Loader -->
		<link rel="stylesheet" href="assets/css/loader.css" />
		<div class="loader">
			<div class="container">
			<h1>🍊</h1>
			<p>摇匀</p>
			<p>再喝</p>
			</div>
		</div>

		<!-- Wrapper -->
			<div id="wrapper">

				<!-- Main -->
					<section id="main">
						<span style="display: flex; position: fixed; top: 2em; right: 2em; opacity: 0.65; font-size: 0.8em;"><button class="button small" @click="screenShot" id="screenshot">保存网页截图</button></span>
						<span style="display: flex; position: fixed; top: 2em; left: 2em; opacity: 0.4;" v-if="backButton"><button class="button small" onclick="toIndex()"><a>&lt;返回</a></button></span>
						<header>
							<span class="avatar"><img src="images/avatar.jpg" alt="" style="width: 122px; height: 122px;" /></span>
							<h1>向 Someone 付款</h1>
							<center class="mountPart" v-if="showAmount">
								<b><span style="font-size: 3em; color: #25b684;">{{ intpart }}.</span><span style="font-size: 2.3em; color: #25b684;">{{ doublepart }}</span><span style="color: #fffff8; font-size: 2em;">元</span></b>
								<p style="color: #cacaca; font-size: 0.9em;">设置的金额</p>
							</center>
							<br />
							<transition-group name="fade">
								<div v-html="text" v-show="defaults" key="text"></div>
								<div v-for="item in method" v-show="item.show" key="item.name" v-html="item.content"></div>
								<button class="button small" key="button" v-show="buttonShow" @click="hideMethod(true)" style="opacity: 0.55;">返回</button>
							</transition-group>
						</header>
						<hr />
						<footer>
							<ul class="icons">
								<li v-for="item in method"><a @click="showMethod(item.name)" :class="item.icon">{{item.desc}}</a></li>
							</ul>
						</footer>
					</section>

				<!-- Footer -->
				<footer id="footer"></footer>

			</div>

		<!-- Scripts -->
			<script>
				if ('addEventListener' in window) {
					window.addEventListener('load', function() { 
						document.body.className = document.body.className.replace(/\bis-preload\b/, ''); 
					});
					document.body.className += (navigator.userAgent.match(/(MSIE|rv:11\.0)/) ? ' is-ie' : '');
				}
			</script>
		<!-- Vue.js -->
			<script>
				var mainsec = new Vue({
					el:'#main',
					data:{
						defaults: 1,

						/*              Change things here             */
						// Show when loaded
						text:"<p>您可以使用微信/支付宝/QQ打开本页面，将会自动跳转到对应二维码<b style=\"color:red;\">不推荐使用<u>QQ钱包</u>付款</b>，<b style=\"color:#4ADEDE;\">推荐使用<u>支付宝</u></p>",
						// Payments
						method:[
							{
								name:"Alipay",
								icon:"icon brands fa-alipay",
								desc:"支付宝",
								content:"<img src=\"images/demoQR.png\" class=\"qr\" /><br /><h2><a href=\"alipayqr://platformapi/startapp?saId=10000007&qrcode=null\"><u>支付宝[点击即可打开]</u></a></h2>",
								show: false
							},
							{
								name:"Wechat",
								icon:"icon brands fa-weixin",
								desc:"微信支付",
								content:"<img src=\"images/demoQR.png\" class=\"qr\" /><br /><h2>微信</h2>",
								show:false
							},
							{
								name:"QQ",
								icon:"icon brands fa-qq",
								desc:"QQ钱包",
								content:"<img src=\"images/demoQR.png\" class=\"qr\" /><br /><h2>QQ钱包</h2>",
								show:false
							}
						],
						/*           Change ends here               */

						buttonShow:0,
						backButton:0,
						deg:0,
						fromurl:"",
						intpart:"",
						doublepart:"",
						showAmount:0
					},
					methods: {
						showMethod: function(name){
							this.defaults = 0
							this.hideMethod(false)
							let num = 0;
							for(num = 0; num < this.method.length; num++){
								if(this.method[num].name == name){
									break;
								}
							}
							clearTimeout(this.timer)
							this.timer = setTimeout(()=>{
								this.method[num].show = true
								this.buttonShow = 1
							},500)
							
						},
						hideMethod: function(neededDefault = true){
							for(let i = 0; i < this.method.length; i++){
								this.method[i].show = false
							}
							clearTimeout(this.timer)
							if(neededDefault == true){
								this.buttonShow = false
								this.timer = setTimeout(()=>{
									this.defaults = 1
								},500)
							}
						},
						rotateCard: function(deg,time,axis){
							this.deg += deg
							var ele = document.getElementById('main')
							ele.style.transition = "all " + time + "s 0s"
							ele.style.transform = "rotate" + axis + "(" +this.deg + "deg)"
						},
						screenShot: function(){
							var button = document.getElementById("screenshot");
							button.disabled = true;
							button.innerHTML = "截图中…";
							html2canvas(document.body).then(canvas => {
								var w = window.open();
								w.document.title = "保存截图";
								var i = w.document.createElement("img");
								w.document.body.appendChild(i);
								i.src = canvas.toDataURL('image/jpg');
								w.alert("长按图片或直接保存图片即可");
								button.innerHTML = "保存网页截图";
								button.disabled = false;
							});
						}
					}
				});
			</script>
			<style>
				.fade-enter-active, .fade-leave-active {
					transition: all .5s;
				}
				.fade-enter, .fade-leave-to {
					opacity: 0;
					transform: scale(0%,0%);
				}
				.qr {
					text-align: center;
					width: 80%;
					height: 80%;
				}
			</style>

	</body>
	<script>
		function loaded(){
			var url;
			var browser = navigator.userAgent.toLowerCase();
			if (browser.match(/MicroMessenger/i) == 'micromessenger') {
				//微信浏览器
				setTimeout(function(){mainsec.showMethod("Wechat")},1500);
			} else if(browser.indexOf(' qq') != -1 && browser.indexOf('mqqbrowser') != -1){
				//qq内置浏览器
				setTimeout(function(){mainsec.showMethod("QQ")},1500);
			} else if(/AlipayClient/.test(window.navigator.userAgent)){
				//支付宝浏览器
				setTimeout(function(){mainsec.showMethod("Alipay")},1500);
				url = "#"; // Your Alipay link
			}
			if(url){
				setTimeout(function(){
					window.location.href=url
				},2500);
			}
			
			if(getQueryVariable("amount")){
				mainsec.showAmount = 1;
				mainsec.intpart = getQueryVariable("amount").split('.')[0];
				mainsec.doublepart = getQueryVariable("amount").split('.')[1];
			}
		}
	</script>
	<script src="js/common.js"></script>
</html>
		
		# imeigancai.github.io








hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh
糖的眼神无辜且甜蜜
经常粘着你 过分想要你
短暂快乐之后会让你后悔莫及
辣的脾气暴躁又尖锐
他令你兴奋 更多是流泪
自诩不是每个人都能和他约会
没有了我的浪漫
他们算什么浪漫
你就只能够抱憾
失去了我的平淡
才是真正的平淡
黑胡椒有高挺的鼻梁
戴着猎鹿帽 骑在牛背上
你说他身上有高贵的香水味道
白胡椒粤语讲的很棒
发烧或感冒 立刻来讨好
温暖的胸膛让你充满幸福气泡
没有了我的浪漫
他们算什么浪漫
你就只能够抱憾
失去了我的平淡
才是真正的平淡
醋你更欣赏的是气质
看上去放肆 却见其别致
最后还心念他幽默绝伦的吐字
我和你像青梅和竹马
会一日三见 会深夜聊天
或许习惯后便对我不再更浓烈
没有了我的浪漫
他们算什么浪漫
你就只能够抱憾
失去了我的平淡
才是真正的平淡
还责备我 总是侃侃而谈一天到晚
没有了我的浪漫
他们算什么浪漫
生活都变得麻烦
失去了我的平淡
才是真正的平淡
还责备我 总是侃侃而谈一天到晚
没有了我的浪漫
他们算什么浪漫
你就只能够抱憾
失去了我的平淡
才是真正的平淡
哈哈
<a url=https://www.baidu.com>百度一下</a>

<a href="https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/video">HTML</a>
