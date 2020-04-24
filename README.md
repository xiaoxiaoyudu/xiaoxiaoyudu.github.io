<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=0">
    <title>myProject</title>
    <!-- 引入 js/echarts.min.js -->
    <script src="js/echarts.min.js"></script>
	<script src="js/mqtt.js"></script>
	<script src="js/jquery.js"></script>
<style>
body{
  background:url("smart.jpg") no-repeat;
  background-size: 100%;
}
.t{
	margin-top:15%;
}
.div1{
	margin-top:100px;
	width:100%;
	 display: flex;
	}
</style>		
</head>
<body>
	<div class="t"><center><h1>水果分拣系统设计</h1></center></div>
 	<div class=div1>
	<img src="apple.jpg" id="userImge" style="width: 50%;height:50%;" name="userImge" >
	<div style="width: 50%;height:200px;">
		<center><h3>第100个苹果</h3></center>
		<center><h3>体积：中</h3></center>
		<center><h3>成熟度：85%</h3></center>
	</div>
    </div>
    <div id="device" style="width: 400px;height:300px;"></div>

	  

    <script>
	//初始化仪表盘
	var myChart = new Array();
	var option = new Array();

	 myChart = echarts.init(document.getElementById('device'));

  option = {
	baseOption: {
			tooltip: {
       		 formatter: '{a} <br/>{b} : {c}%'
			},
			toolbox: {
				feature: {
          	  	restore: {},
          	 	 saveAsImage: {}
				}
			},
			series: [
       		 {
            name: '成熟度',
            type: 'gauge',
            detail: {formatter: '{value}%'},
            data: [{value: 50, name: '成熟度'}]
       		 }
			]
		},
	       media: [
            {
            	 query: {    minWidth: 300,
  					
   							},
                option: {
					toolbox: {
						//center: ['25%', '50%']
						},
				series: [
      				  {

          				 center: ['50%', '50%'],
          				 width:'80%'
       				  }
						]
         		 	  },
  
         	 }
      	  ]
		
}
//初始化mqtt信息		
const options = {
      // 认证信息
      clientId: 'web',
      username: 'wlw',
      password: 'wlw12138',
}

const client = mqtt.connect('ws://47.92.29.155:8083/mqtt', options)
client.subscribe('sw_led')

myChart.setOption(option, true)
//接受信息
client.on('message', function (topic, message) {
	var p1 = message.toString();
 	 //console.log(p1);

	if(topic=="sw_c")
	{
		var p2 = JSON.parse(p1);
 
  
	v1=p1.message.vaule;
		console.log(v1); 
		var name; 
		switch(p1.message.type)
	{
		case '1':
			name="温度";
        break;
		case '2':
			name="湿度";
        break;
		default:
			name="未知设备"
		break;
        
		}
	console.log(name);
	option.series.data.value = v1;
	option.series.data.name = name;
	myChart.setOption(option, true)
	}
	else
	{
		$("#userImge").attr("src","data:image/jpg;base64,"+p1);
	}
  
	 
  
})
client.on('reconnect', (error) => {
    console.log('正在重连:', error)
})
client.on('connect', (error) => {
    console.log('连接成功:', error)
})
client.on('error', (error) => {
    console.log('连接失败:', error)
})
  </script>
</body>
</html>
