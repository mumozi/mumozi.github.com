# JQ插件工具

## [原生js简单日期](https://www.jq22.com/jquery-info15933)

```html
<!-- 日历 -->
<div class="single-schedule">
    <div id='schedule-box' class="boxshaw">
    </div>
</div>
<script>
$(function(){
    var mySchedule = new Schedule({
        el: '#schedule-box',    //容器元素
        date: sugar.today(),     //当前日期
        // disabledBefore: '2018-07-10',   //禁用此日期之前
        // disabledAfter: '2018-11-15',    //禁用此日期之后
        // disabledDate: ['2018-8-20', '2018-8-2', '2018-8-23'],   //禁用的日期
        selectedDate: {$date_list|raw},   //选中的日期
        // showToday: true,    //回到今天
        clickCb: function (date, name) {
            if(name.indexOf('selected-style') > -1){
                getDate(date);
                // console.log(date);
            }
            // document.querySelector('#h3Ele').innerHTML = '日期：' + date
        }
        // nextMonthCb: function (currentYear, currentMonth) {

        //     console.log('currentYear:' + currentYear, 'currentMonth:' + currentMonth)
        // },
        // nextYeayCb: function (currentYear, currentMonth) {
        //     console.log('currentYear:' + currentYear, 'currentMonth:' + currentMonth)
        // },
        // prevMonthCb: function (currentYear, currentMonth) {
        //     // this.selectedDate = getEventsDate(currentYear+"-"+currentMonth+"-1");
        //     console.log('y:' + currentYear, 'm:' + currentMonth)
        // },
        // prevYearCb: function (currentYear, currentMonth) {
        //     console.log('currentYear:' + currentYear, 'currentMonth:' + currentMonth)
        // }
    });

})
</script>
```

## [编辑器引入wangeditor](https://www.wangeditor.com/)

> 封装组件，增加html源码模式（其中为预防初始化显示html，会预处理）

```javascript
var star = {
    /**
	 * [引入wangeditor]
	 * @Author sugar
	 * @date   2020-11-26
	 * @param  {String}   divId     [编辑器div的id]
	 * @param  {String}   imgUrl    [上传图片地址]
	 * @param  {String}   height    [高度]
	 * @param  {String}   contentId [表单textarea的id]
	 * @return {[type]}             [description]
	 */
	editor : function(divId, contentId, height){
		// 富文本编辑器开始
	    const E = window.wangEditor
	    const editor = new E('#' + divId)
	    // 定义菜单html
	    
		// 定义菜单
		class CustomMenu extends E.BtnMenu {
		    constructor(editor) {
		        const $elem = E.$(
		            `<div class="w-e-menu">
		                <a href="javaScript:;" id="menu_y" class="">html</a>
		            </div>`
		        )
		        super($elem, editor)
		    }
		 
		    // 【源】点击事件
		    clickHandler() {
		        var editorHtml = editor.txt.html();
		        if ($("#menu_y").attr("class") == "active_y") {
		            $("#menu_y").attr("class", "")
		            editorHtml = editor.txt.text().replace(/&lt;/ig, "<").replace(/&gt;/ig, ">").replace(/&nbsp;/ig, " ");
		        } else {
		            $("#menu_y").attr("class", "active_y")
		            editorHtml = editorHtml.replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/ /g, "&nbsp;");
		        }
		        editor.txt.html(editorHtml);
		        editor.change && editor.change();
		    }
		    tryChangeActive() {
		        this.active();
		    }
		}

	    // 设置编辑区域高度为 500px
	    editor.config.height = height;
	    // 配置 server 接口地址
	    editor.config.uploadImgServer = '/star/Upload/uploadEditor';
	    editor.config.showLinkImg = false; // 即可隐藏插入网络图片的功能
	    editor.config.uploadImgMaxLength = 5 // 一次最多上传 5 个图片
	    editor.config.uploadFileName = 'file'
	    editor.config.uploadImgParams = {
	        type: 'editor' // 属性值会自动进行 encode ，此处无需 encode
	    };
	    // 关闭粘贴样式的过滤
	    // editor.config.pasteFilterStyle = false
	    // 忽略粘贴内容中的图片
	    editor.config.pasteIgnoreImg = true
	    // 自定义处理粘贴的文本内容
	    editor.config.pasteTextHandle = function(lineContent) {
	        // content 即粘贴过来的内容（html 或 纯文本），可进行自定义处理然后返回
	        if (lineContent == '' && !lineContent) return ''
	        var str = lineContent
	        str = str.replace(/<xml>[\s\S]*?<\/xml>/ig, '')
	        str = str.replace(/<style>[\s\S]*?<\/style>/ig, '')
	        // str = str.replace(/<\/?[^>]*>/g, '')
	        // str = str.replace(/[ | ]*\n/g, '\n')
	        // str = str.replace(/&nbsp;/ig, '')
	        return str
	    }
	    // 自定义弹窗
	    editor.config.customAlert = function(s) {
	        layer.msg(s, {icon: 5,time: 1500,shade: 0.1});
	    }
	    const $content = $('#' + contentId);
	    editor.config.onchange = function(html) {
	        // 第二步，监控变化，同步更新到 textarea
	        $content.val(html)
	    }
		// 注册菜单
		const customMenuKey = 'customMenuKey';
		editor.menus.extend('customMenuKey', CustomMenu);
		editor.config.menus = editor.config.menus.concat(customMenuKey);

	    editor.create()
	    //设置初始文本
	    let con = $content.val();
	    // 防止存入数据库是源码
	    con = con.replace(/&lt;/ig, "<").replace(/&gt;/ig, ">").replace(/&nbsp;/ig, " ");
	    editor.txt.html(con)
	    // 第一步，初始化 textarea 的值 (新增开启，编辑关闭)   
	    // $content.val(editor.txt.html())
	    // 富文本编辑器结束
	},
}
```

> 使用方法

```html
<div id="tab-3" class="tab-pane">
<div class="hr-line-dashed"></div>                          
    <div class="form-group">
        <label class="col-sm-3 control-label ">活动内容：</label>
        <div class="input-group col-sm-8">
            <textarea name="content" style="display:none;" id="content" ></textarea>
            <div id="div1"  style="width:93%;">
            </div>   
        </div>
    </div>
</div>

<script type="text/javascript">
    star.editor('div1', 'content', 800);
</script>
```

