# 模板

## 下拉选择——默认

```php
<div class="col-sm-2">
    <select class="form-control m-b chosen-select col-md-4" name="status" id="status">
        <option value="">==选择核对==</option>
        <option value="未沟通" {eq name="Request.get.status|default=$status" value="未沟通"}selected{/eq}>未沟通</option>
        <option value="未接通" {eq name="Request.get.status|default=$status" value="未接通"}selected{/eq}>未接通</option>
        <option value="正确" {eq name="Request.get.status|default=$status" value="正确"}selected{/eq}>正确</option>
        <option value="更新" {eq name="Request.get.status|default=$status" value="更新"}selected{/eq}>更新</option>
        <option value="删除" {eq name="Request.get.status|default=$status" value="删除"}selected{/eq}>删除</option>
    </select>
</div>

<script type="text/javascript">
	var status={$Request.get.status|default="$('#status').val()"};
</script>
```

## 文件下载阅读方式

```html
<!-- 直接下载文件 -->
<a href="/CH/JournaPic/pdf/{$b.book_pdf_4}" download="{$b.book_pdf_4}.pdf"  target="view_window">行业展望</a>
<!-- 直接阅读 -->
<div class="bt04" onclick='readPdf("{$b.book_pdf_4}")'>
	<img src="/static/home/images/media_kit_1_04.png">
	<div class="bt">行业展望</div>
<script type="text/javascript">
//新页面直接阅读
window.open('/CH/JournaPic/pdf/'+bookpdf);
//原地址直接阅读
window.location.href=();
    
    function readPdf(bookpdf){
		window.open(+bookpdf);
	}
</script>
```

## 项目部署404找不到

### 问题现象：

访问配置的虚拟目录时，可以访问到框架的入口文件，但无法访问到虚拟目录对应的模块（启用了项目分组）。

### 问题出现原因：

集成环境自动生成的`vhost.conf`文件中缺少两行配置语句句

```shell
if (!-e $request_filename) {

	rewrite ^/(.*)$ /index.php/$1 last;

}
```

## 点击按钮内容复制到剪切板

```php
<a href="javascript:;" onclick="copyUrl({{d[i].id}})" class="btn btn-success btn-xs">
   <i class="fa fa-link"></i> 复制地址</a>
    
<!-- 透明的剪辑版 -->
<input id="hide" type="text" style='opacity: 0'/>
    
<script type="text/javascript">   
  // 剪辑版获取地址url
function copyUrl(id){
    $("#hide").val({:url('/article/')}+id);//这里可以获取动态数据赋值给$("#hide").val()
    $("#hide").select();
    try {
        var state = document.execCommand("copy");
    }catch(err){
        var state = false;
    }
    if(state){
        layer.msg("复制成功！");
    }else{
        layer.msg("复制失败！");
    }
};
</script>
```

