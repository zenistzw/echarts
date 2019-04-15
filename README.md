# echarts
PHP+MySQL+Ajax+echarts实现统计图
php mysql echarts动态生成图表，数据库和表格使用的java Servlet echarts 图表生成的一样数据，不知道的看我前面一篇博文；需要两个文件完成

1. 数据获取文件 weekbar.php

代码如下：

<?php  
header("Content-type=text/json;charset=UTF-8");  
  
$conn = @mysql_connect("127.0.0.1","root","root") or die("无法连接数据库...");  
mysql_query("set names utf-8");  
mysql_select_db("test",$conn);
$resultset = mysql_query('SELECT name, num FROM bar ');  
$data = array();  
class Alteration{  
    public $name;  
    public $num;  
}  
  
while($row = mysql_fetch_array($resultset,MYSQL_ASSOC)) {  
    $alter = new Alteration();  
    $alter->name = $row['name'];   
    $alter->num = $row['num'];  
    $data[] = $alter;  
} 
/*依次存入shop和num列到alyer()*/  
  
echo json_encode($data);
mysql_close($conn);  
  
?>


2.数据图表展示页面echart.php

代码如下：

<!DOCTYPE html>  
<html>  
<head>  
    <meta charset="utf-8">  
    <title>ECharts</title>  
    <!-- 引入 echarts.js -->  
<script src="https://cdn.bootcss.com/echarts/3.5.4/echarts.js"></script>  
    <script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>  
  
</head>  
<body>  
    <!-- 为ECharts准备一个具备大小（宽高）的Dom -->  
    <div id="container" style="width: 600px;height:400px;"></div>  
   <script>


    // 初始化两个数组，盛装从数据库中获取到的数据
    var names = [], nums = [];


    //调用ajax来实现异步的加载数据
    function getusers() {
        $.ajax({
            type: "post",
            async: false,
            url: "weekbar.php",
            data: {},
            dataType: "json",
            success: function(result){
                if(result){
                    for(var i = 0 ; i < result.length; i++){
                        names.push(result[i].name);
                        nums.push(result[i].num);
                    }
                }
            },
            error: function(errmsg) {
                alert("Ajax获取服务器数据出错了！"+ errmsg);
            }
        });
    return names, nums;
    }


    // 执行异步请求
    getusers();




    // 初始化 图表对象
        var mychart = echarts.init(document.getElementById("container"));
        // 进行相关项的设置，也就是所谓的搭搭骨架，方便待会的ajax异步的数据填充
        var option = {
            title : {
                text : '姓名数量分布图'
            },
            tooltip : {
                show : true
            },
            legend : {
                data : [ '' ]
            },
            xAxis : [ {
                data : names
            } ],
            yAxis : [ {
                type : 'value'
            } ],
            series : [ {
                "name" : "",
                "type" : "bar",
                "data" : nums
            } ]
        };


        // 将配置项赋给chart对象，来显示相关的数据
        mychart.setOption(option);








    </script>
</body>  

</html>  



运行效果：



