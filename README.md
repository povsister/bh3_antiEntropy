# bh3_antiEntropy
A js script to auto-get all collections for bh3 Anti-Entropy adventure game

> 自动完成崩崩崩逆熵视觉小说成就的JavaScript脚本

## Usage

* 获取自己游戏帐号的auth_key与sign hash，可以[参考这里](http://bbs.nga.cn/read.php?tid=14608507)
* 使用浏览器(Chrome kernel)登录 https://event.bh3.com/avgAntiEntropy/index.php?auth_key=XXX&sign=XXX
* **随意点开一章，阅读几页，以便自动初始化部分全局变量**
* F12进入开发者模式
* **Ctrl+F8 取消所有断点调试。即toggle Activate breakpoint**
* **再次刷新页面 或 按F8 或 点击蓝色箭头 继续脚本执行**
* 在Console键入以下js代码并回车。**若出现操作频繁等问题，请参考Notice部分**
```
function my_post_achievement(str_ach, now_galgame, now_scene){
    var ajax_answer = null;
    var ajax_async = false;
    $.ajax({
        type:"POST",
        url:"./utils/achievement.php" + achievementQueryString,
        dataType:"json",
        data:{achievement:str_ach,
            chapter:now_galgame,
            scene:now_scene},
        async:ajax_async,
        success:function(result) {
            $('.award-log').append("章节"+now_galgame+"场景"+now_scene+". 状态："+result.msg+"\n code:"+result.achievement+"</br>");
        },
        error:function(XMLHttpRequest, textStatus, errorThrown){
            console.log(XMLHttpRequest);
            console.log(textStatus);
            console.log(errorThrown);
        } 
        });
    return ajax_answer;
}

function loadXmlFile(xmlFile)
{   
    var xmlDom = null;
    if (window.ActiveXObject)
    {
        xmlDom = new ActiveXObject("Microsoft.XMLDOM");
        xmlDom.async="false";
        xmlDom.load(xmlFile);
    }
    else if(document.implementation && document.implementation.createDocument)
    {
        var xmlhttp = new window.XMLHttpRequest();
        xmlhttp.open("GET", xmlFile, false);
        xmlhttp.send(null);
        xmlDom = xmlhttp.responseXML;
    }
    else
    {
        xmlDom = null;
        
    }
    return xmlDom;
}

function galgame(name){
    let i,j;
    let now_galgame  = Number(name);
    let xmlDoc = loadXmlFile("./lang_CN/xml/ch"+name+".xml?sid="+Math.random());

    let sceneList0 = [];
    sceneList0 = xmlDoc.getElementsByTagName('scene');    
    let sceneList = new Array();    
    for(i = 0;sceneList0[i] != null;i++){
    	j = sceneList0[i].getAttribute("id");
    	sceneList[Number(j)] = sceneList0[i];
    	sceneList0[i] = null;
    }    	
    pa(sceneList, now_galgame)
}

function post_achievement_in_event(eventNode, now_galgame, now_scene){
    if(eventNode.getAttribute("post")){
        my_post_achievement(eventNode.getAttribute("post"), now_galgame, now_scene);
    }	
}

function systemAutoSave(now_galgame, now_scene){
    setCookie(now_galgame_tag, now_galgame);
    setCookie(now_scene_tag, now_scene);
}

function pa(sceneList, now_galgame) {
    for (sceneIndex = 0; sceneIndex < sceneList.length; sceneIndex++) {
        if (sceneList[sceneIndex]) {
            let thisScene = sceneList[sceneIndex];
            let now_scene = sceneIndex;
            for (i = 0; i < thisScene.childNodes.length; i++) {
                let thisEvent = thisScene.childNodes[i];
                systemAutoSave(now_galgame, now_scene);
                if (thisEvent.nodeName == "remark") {
                    post_achievement_in_event(thisEvent, now_galgame, now_scene);
                }
                let thisTag = thisEvent.tagName;
                if (thisTag == "end" || thisTag == "speak" || thisTag == "text") {
                    post_achievement_in_event(thisEvent, now_galgame, now_scene);
                }
            }
        }
    }
}
for (let itl = 1; itl < 1000; itl++) {
    clearInterval(itl);
}
$('.black').css('overflow', 'scroll');
$('.black').append('<div style="position:absolute; left:0px; top:0px; text-align:left; margin:5px; background-color:white; z-index:1000" class="award-log">Log output</br></div>');

for (let i = 1; i <= 26; i++) {
    galgame(i);
}
```

## Notice

仅在Chrome 68.0.3440.75 上测试通过。其他浏览器不保证兼容性

部分代码摘抄自米忽悠前端，风格不保证统一

**若出现操作频繁等问题，请手动修改上方代码最后一个for循环的起始及终止条件，间隔一段时间（半小时以上）分批次提交成就**

**例如**
```
for (let i = 1; i <= 8; i++) {
    galgame(i);
}
```
**代表本次仅完成1-8章的所有成就**

## License

[WTFPL License](https://en.wikipedia.org/wiki/WTFPL)
