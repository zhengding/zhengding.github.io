---
title: SocialCalc即时渲染
data:  2018-01-30 09:09:01
tag:
- SocialCalc
categories: 前端
---

# SocialCalc即时渲染简介

**作者**：[DanBricklin](https://github.com/DanBricklin)

**源码地址：** [https://github.com/DanBricklin/socialcalc](https://link.jianshu.com/?t=http://jsfiddle.net/audreyt/LtDyP/)

**使用技术：** JavaScript+HTML+CSS

**主界面截图:**

![SocialCalc主页面](http://upload-images.jianshu.io/upload_images/10390285-ca9e2adf0d08f588.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

初始化sheet

创建sheet

```javascript
    SocialCalc.CreateTableEditor = function (editor, width, height) {}
```

## 一、初始化参数

```javascript
var scc = SocialCalc.Constants;
var AssignID = SocialCalc.AssignID;

editor.toplevel = document.createElement("div");
editor.width = width;
editor.height = height;

editor.griddiv = document.createElement("div");
editor.tablewidth = width - scc.defaultTableControlThickness;
editor.tableheight = height - scc.defaultTableControlThickness;
editor.griddiv.style.width = editor.tablewidth + "px";
editor.griddiv.style.height = editor.tableheight + "px";
editor.griddiv.style.overflow = "hidden";
editor.griddiv.style.cursor = "default";
if (scc.cteGriddivClass) editor.griddiv.className = scc.cteGriddivClass;
AssignID(editor, editor.griddiv, "griddiv");
```

## 二、适配屏幕大小，设置页面显示行数、列数

```javascript
editor.FitToEditTable();
```
​	核心代码

```javascript
totalwidth = context.showRCHeaders ? context.rownamewidth - 0 : 0;
for (colpane = 0; colpane < context.colpanes.length - 1; colpane++) { // Get width of all but last pane
    for (colnum = context.colpanes[colpane].first; colnum <= context.colpanes[colpane].last; colnum++) {
        colname = SocialCalc.rcColname(colnum);
        colwidth = sheetobj.colattribs.width[colname] || sheetobj.attribs.defaultcolwidth || SocialCalc.Constants.defaultColWidth;
        if (colwidth == "blank" || colwidth == "auto") colwidth = "";
        totalwidth += (colwidth && ((colwidth - 0) > 0)) ? (colwidth - 0) : 10;
    }
}

for (colnum = context.colpanes[colpane].first; colnum <= 10000; colnum++) { //!!! max for safety, but makes that col max!!!
    colname = SocialCalc.rcColname(colnum);
    colwidth = sheetobj.colattribs.width[colname] || sheetobj.attribs.defaultcolwidth || SocialCalc.Constants.defaultColWidth;
    if (colwidth == "blank" || colwidth == "auto") colwidth = "";
    totalwidth += (colwidth && ((colwidth - 0) > 0)) ? (colwidth - 0) : 10;
    if (totalwidth > editor.tablewidth) break;
}

context.colpanes[colpane].last = colnum;

// Calculate row height data

totalrows = context.showRCHeaders ? 1 : 0;
for (rowpane = 0; rowpane < context.rowpanes.length - 1; rowpane++) { // count all panes but last one
    totalrows += context.rowpanes[rowpane].last - context.rowpanes[rowpane].first + 1;
}

needed = editor.tableheight - totalrows * context.pixelsPerRow; // estimate amount needed

context.rowpanes[rowpane].last = context.rowpanes[rowpane].first + Math.floor(needed / context.pixelsPerRow) + 1;
```

## 三、渲染sheet，更新fullgrid

```javascript
editor.EditorRenderSheet();
```
```javascript
SocialCalc.EditorRenderSheet = function (editor) {

    editor.EditorMouseUnregister();

    editor.fullgrid = editor.context.RenderSheet(editor.fullgrid);

    if (editor.ecell) editor.SetECellHeaders("selected");

    SocialCalc.AssignID(editor, editor.fullgrid, "fullgrid"); // give it an id

    editor.EditorMouseRegister();

}
```

### 1、取消editor鼠标事件

### 2、渲染sheet

​	一系列的sheet渲染操作，代码略

### 3、为fullgrid分配id

### 4、重新注册editor鼠标事件

​	设置鼠标单击、双击等事件

```javascript
if (element.addEventListener) { // DOM Level 2 -- Firefox, et al
    element.addEventListener("mousedown", SocialCalc.ProcessEditorMouseDown, false);
    element.addEventListener("dblclick", SocialCalc.ProcessEditorDblClick, false);
}
else if (element.attachEvent) { // IE 5+
    element.attachEvent("onmousedown", SocialCalc.ProcessEditorMouseDown);
    element.attachEvent("ondblclick", SocialCalc.ProcessEditorDblClick);
}
```

## 四、添加fullgrid节点

```javascript
editor.griddiv.appendChild(editor.fullgrid);
```
## 五、纵向、横向滚动条配置

```javascript
//纵向滚动条配置
editor.verticaltablecontrol = new SocialCalc.TableControl(editor, true,         
editor.tableheight);
editor.verticaltablecontrol.CreateTableControl();
AssignID(editor, editor.verticaltablecontrol.main, "tablecontrolv");

//横向滚动条配置
editor.horizontaltablecontrol = new SocialCalc.TableControl(editor, false, 
editor.tablewidth);
editor.horizontaltablecontrol.CreateTableControl();
AssignID(editor, editor.horizontaltablecontrol.main, "tablecontrolh");
```

## 六、table配置及渲染


```javascript
var table, tbody, tr, td, img, anchor, ta;
table = document.createElement("table");
editor.layouttable = table;
table.cellSpacing = 0;
table.cellPadding = 0;
AssignID(editor, table, "layouttable");

tbody = document.createElement("tbody");
table.appendChild(tbody);

//table
tr = document.createElement("tr");
tbody.appendChild(tr);
td = document.createElement("td");
td.appendChild(editor.griddiv);
tr.appendChild(td);
```

## 七、纵向、横向滚动条渲染

```javascript
//纵向滚动条
td = document.createElement("td");
td.appendChild(editor.verticaltablecontrol.main);
tr.appendChild(td);

//横向滚动条
tr = document.createElement("tr");
tbody.appendChild(tr);
td = document.createElement("td");
td.appendChild(editor.horizontaltablecontrol.main);
tr.appendChild(td);
td = document.createElement("td"); // logo display: Required by CPAL License for this code!
td.style.background = "url(" + editor.imageprefix + "logo.gif) no-repeat center center";
td.innerHTML = "<div style='cursor:pointer;font-size:1px;'><img src='" + editor.imageprefix + "1x1.gif' border='0' width='18' height='18'></div>";
tr.appendChild(td);
editor.logo = td;
AssignID(editor, editor.logo, "logo");
SocialCalc.TooltipRegister(td.firstChild.firstChild, "SocialCalc", null);
```

## 八、添加table节点

```javascript
editor.toplevel.appendChild(editor.layouttable);
```
## 九、添加滚动条监听事件，实现滚动即时渲染

```javascript
// sheet滚动操作 重点*****
SocialCalc.MouseWheelRegister(editor.toplevel, {WheelMove:     
SocialCalc.EditorProcessMouseWheel, editor: editor});
```

### 1、其中参数：

#### 1.1、editor.toplevel

#### 1.2、WheelMove:SocialCalc.EditorProcessMouseWheel

##### 	1.2.1 判断editor此时状态，true则返回

##### 	1.2.2判断滚动方式为垂直还是水平，垂直则上下加减1，水平则左右加减1

```javascript
SocialCalc.EditorProcessMouseWheel = function (event, delta, mousewheelinfo, wobj) {

    if (wobj.functionobj.editor.busy) return; // ignore if busy

    if (delta > 0) {
        wobj.functionobj.editor.ScrollRelative(true, -1);
    }
    if (delta < 0) {
        wobj.functionobj.editor.ScrollRelative(true, +1);
    }

}
```

##### 	1.2.3如果垂直、水平方向都滚动，调用ScrollRelativeBoth

```javascript
SocialCalc.ScrollRelativeBoth = function (editor, vamount, hamount) {

    var context = editor.context;

    var vplen = context.rowpanes.length;
    var vlimit = vplen > 1 ? context.rowpanes[vplen - 2].last + 1 : 1; // don't scroll past here
    if (context.rowpanes[vplen - 1].first + vamount < vlimit) { // limit amount
        vamount = (-context.rowpanes[vplen - 1].first) + vlimit;
    }

    var hplen = context.colpanes.length;
    var hlimit = hplen > 1 ? context.colpanes[hplen - 2].last + 1 : 1; // don't scroll past here
    if (context.colpanes[hplen - 1].first + hamount < hlimit) { // limit amount
        hamount = (-context.colpanes[hplen - 1].first) + hlimit;
    }

    if ((vamount == 1 || vamount == -1) && hamount == 0) { // special case quick scrolls
        if (vamount == 1) {
            editor.ScrollTableUpOneRow();
        }
        else {
            editor.ScrollTableDownOneRow();
        }
        if (editor.ecell) editor.SetECellHeaders("selected");
        editor.SchedulePositionCalculations();
        return;
    }

    // Do a gross move and render

    if (vamount != 0 || hamount != 0) {
        context.rowpanes[vplen - 1].first += vamount;
        context.rowpanes[vplen - 1].last += vamount;
        context.colpanes[hplen - 1].first += hamount;
        context.colpanes[hplen - 1].last += hamount;
        editor.FitToEditTable();
        editor.ScheduleRender();
    }

}
```

#### 1.3、editor

### 2、实现流程：

#### 2.1、将监听节点放入监听数组中

```javascript
var mousewheelinfo = SocialCalc.MouseWheelInfo;

mousewheelinfo.registeredElements.push(
    {element: element, functionobj: functionobj}
);
```

#### 2.2、兼容浏览器设置监听事件ProcessMouseWheel

```javascript
if (element.addEventListener) { // DOM Level 2 -- Firefox, et al
    element.addEventListener("DOMMouseScroll", SocialCalc.ProcessMouseWheel, false);
    element.addEventListener("mousewheel", SocialCalc.ProcessMouseWheel, false); // Opera needs this
}
else if (element.attachEvent) { // IE 5+
    element.attachEvent("onmousewheel", SocialCalc.ProcessMouseWheel);
}
else { // don't handle this
    throw SocialCalc.Constants.s_BrowserNotSupported;
}
```

##### 2.2.1监听事件ProcessMouseWheel流程

```javascript
SocialCalc.ProcessMouseWheel = function (e) {
    var event = e || window.event;
    var delta;

    if (SocialCalc.Keyboard.passThru) return; // ignore

    var mousewheelinfo = SocialCalc.MouseWheelInfo;

    var ele = event.target || event.srcElement; // source object is often within what we want
    var wobj;

    for (wobj = null; !wobj && ele; ele = ele.parentNode) { // go up tree looking for one of our elements
        wobj = SocialCalc.LookupElement(ele, mousewheelinfo.registeredElements);
    }
    if (!wobj) return; // not one of our elements

    if (event.wheelDelta) {
        delta = event.wheelDelta / 120;
    }
    else delta = -event.detail / 3;
    if (!delta) delta = 0;

    if (wobj.functionobj && wobj.functionobj.WheelMove) wobj.functionobj.WheelMove(event, delta, mousewheelinfo, wobj);

    if (event.preventDefault) event.preventDefault();
    event.returnValue = false;

}
```