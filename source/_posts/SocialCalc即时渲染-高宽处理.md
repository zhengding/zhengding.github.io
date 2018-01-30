---
title: SocialCalc即时渲染
data:  2018-01-30 13:50:01
tag:
- SocialCalc
categories: 前端
---

# SocialCalc即时渲染-高宽处理

## 一、主页面socialcalc2demo-0-8-1.html调用

```javascript
spreadsheet.InitializeSpreadsheetControl("tableeditor");
```

## 二、调用socialcalcspreadsheetcontrol中InitializeSpreadsheetControl方法

```javascript
SocialCalc.InitializeSpreadsheetControl = function (spreadsheet, node, height, width, spacebelow){}
```

### 1、初始化requestedHeight、requestedWidth、requestedSpaceBelow，方法中height、width、spacebelow均为undefined

```javascript
spreadsheet.requestedHeight = height;
spreadsheet.requestedWidth = width;
spreadsheet.requestedSpaceBelow = spacebelow;
```

### 2、计算主页面高度、宽度

```javascript
spreadsheet.SizeSSDiv(); // calculate and fill in the size values
```

#### 2.1、得到spreadsheet.height整个页面高度，spreadsheet.width整个页面宽度

```javascript
SocialCalc.SizeSSDiv = function (spreadsheet) {
    var sizes, pos, resized, nodestyle, newval;
    var fudgefactorX = 10; // for IE
    var fudgefactorY = 10;

    resized = false;

    sizes = SocialCalc.GetViewportInfo();
    pos = SocialCalc.GetElementPosition(spreadsheet.parentNode);
    pos.bottom = 0;
    pos.right = 0;

    nodestyle = spreadsheet.parentNode.style;

    if (nodestyle.marginTop) {
        pos.top += nodestyle.marginTop.slice(0, -2) - 0;
    }
    if (nodestyle.marginBottom) {
        pos.bottom += nodestyle.marginBottom.slice(0, -2) - 0;
    }
    if (nodestyle.marginLeft) {
        pos.left += nodestyle.marginLeft.slice(0, -2) - 0;
    }
    if (nodestyle.marginRight) {
        pos.right += nodestyle.marginRight.slice(0, -2) - 0;
    }
  
    newval = spreadsheet.requestedHeight ||
        sizes.height - (pos.top + pos.bottom + fudgefactorY) -
        (spreadsheet.requestedSpaceBelow || 0);

    if (spreadsheet.height != newval) {
        spreadsheet.height = newval;
        spreadsheet.spreadsheetDiv.style.height = newval + "px";
        resized = true;
    }

    newval = spreadsheet.requestedWidth ||
        sizes.width - (pos.left + pos.right + fudgefactorX) || 700;

    if (spreadsheet.width != newval) {
        spreadsheet.width = newval;
        spreadsheet.spreadsheetDiv.style.width = newval + "px";
        resized = true;
    }

    return resized;

}
```

### 3、计算表格宽度、高度

```javascript
	// create sheet view and others
    // spreadsheet.statuslineheight
    // spreadsheet.spreadsheetDiv.firstChild.offsetHeight-工具栏高度
    // spreadsheet.spreadsheetDiv.lastChild.offsetHeight-公式框高度
    spreadsheet.nonviewheight = spreadsheet.statuslineheight +
        spreadsheet.spreadsheetDiv.firstChild.offsetHeight +
        spreadsheet.spreadsheetDiv.lastChild.offsetHeight;
    spreadsheet.viewheight = spreadsheet.height - spreadsheet.nonviewheight;
    // spreadsheet.width = width
    // spreadsheet.viewheight = height
    spreadsheet.editorDiv = spreadsheet.editor.CreateTableEditor(spreadsheet.width, spreadsheet.viewheight);
```

## 三、调用CreateTableEditor方法渲染sheet

```javascript
SocialCalc.CreateTableEditor = function (editor, width, height) {}
```

### 1、初始化参数

```javascript
var scc = SocialCalc.Constants;
var AssignID = SocialCalc.AssignID;

// spreadsheet.width = width
// spreadsheet.viewheight = height
editor.toplevel = document.createElement("div");
editor.width = width;
editor.height = height;

editor.griddiv = document.createElement("div");
//scc.defaultTableControlThickness滚动条的大小
editor.tablewidth = width - scc.defaultTableControlThickness;
editor.tableheight = height - scc.defaultTableControlThickness;
editor.griddiv.style.width = editor.tablewidth + "px";
editor.griddiv.style.height = editor.tableheight + "px";
editor.griddiv.style.overflow = "hidden";
editor.griddiv.style.cursor = "default";
if (scc.cteGriddivClass) editor.griddiv.className = scc.cteGriddivClass;
AssignID(editor, editor.griddiv, "griddiv");
```

### 2、适配屏幕大小，设置页面显示行数、列数

```javascript
editor.FitToEditTable();
```

```javascript
SocialCalc.FitToEditTable = function (editor) {
    // console.log('FitToEditTable............')
    var colnum, colname, colwidth, totalwidth, totalrows, rowpane, needed;

    var context = editor.context;
    var sheetobj = context.sheetobj;
    // var sheetcolattribs = sheetobj.colattribs;

    // 计算列宽
    // context.showRCHeaders 是否展示首行首列
    // ontext.rownamewidth 首列宽度
    // totalwidth 需要显示的表格宽度
    totalwidth = context.showRCHeaders ? context.rownamewidth - 0 : 0;
    //获取除了最后一个单元格的总宽度
    console.log(context.colpanes.length)
    for (colpane = 0; colpane < context.colpanes.length - 1; colpane++) { // Get width of all but last pane
        for (colnum = context.colpanes[colpane].first; colnum <= context.colpanes[colpane].last; colnum++) {
            // 获取该单元格name
            colname = SocialCalc.rcColname(colnum);
            // 获取该单元格width
            colwidth = sheetobj.colattribs.width[colname] || sheetobj.attribs.defaultcolwidth || SocialCalc.Constants.defaultColWidth;
            if (colwidth == "blank" || colwidth == "auto") colwidth = "";
            totalwidth += (colwidth && ((colwidth - 0) > 0)) ? (colwidth - 0) : 10;
        }
    }

    for (colnum = context.colpanes[colpane].first; colnum <= 10000; colnum++) { //!!! max for safety, but makes that col max!!!
        // 获取该单元格name
        colname = SocialCalc.rcColname(colnum);
        // 获取该单元格width
        colwidth = sheetobj.colattribs.width[colname] || sheetobj.attribs.defaultcolwidth || SocialCalc.Constants.defaultColWidth;
        if (colwidth == "blank" || colwidth == "auto") colwidth = "";
        // 计算需要显示的表格宽度
        totalwidth += (colwidth && ((colwidth - 0) > 0)) ? (colwidth - 0) : 10;
        // 当totalwidth大于实际宽度editor.tablewidth时结束，此时colnum即为最后colnum
        if (totalwidth > editor.tablewidth) break;
    }

    context.colpanes[colpane].last = colnum;

    // 计算行高
    // context.showRCHeaders 是否显示首行首列
    totalrows = context.showRCHeaders ? 1 : 0;
    for (rowpane = 0; rowpane < context.rowpanes.length - 1; rowpane++) { // count all panes but last one
        totalrows += context.rowpanes[rowpane].last - context.rowpanes[rowpane].first + 1;
    }

    // pixelsPerRow 默认行高
    needed = editor.tableheight - totalrows * context.pixelsPerRow; // estimate amount needed
    context.rowpanes[rowpane].last = context.rowpanes[rowpane].first + Math.floor(needed / context.pixelsPerRow) + 1;
}
```

## 四、添加滚动条监听事件，实现滚动即时渲染

```javascript
// sheet滚动操作
SocialCalc.MouseWheelRegister(editor.toplevel, {WheelMove:     
SocialCalc.EditorProcessMouseWheel, editor: editor});
```

#### 1、配置鼠标滚动监听事件

```javascript
 SocialCalc.MouseWheelRegister = function (element, functionobj) {
    var mousewheelinfo = SocialCalc.MouseWheelInfo;

    mousewheelinfo.registeredElements.push(
        {element: element, functionobj: functionobj}
    );

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

    return;

}xxxxxxxxxx var mousewheelinfo = SocialCalc.MouseWheelInfo;mousewheelinfo.registeredElements.push(    {element: element, functionobj: functionobj});

```

### 2、监听事件ProcessMouseWheel

1、获取滚动滚动对象event、滚动个数delta、鼠标滚动信息mousewheelinfo、sheet等对象信息wobj

2、调用WheelMove方法，即SocialCalc.EditorProcessMouseWheel方法，见下2.2

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

### 2、其中参数：

#### 2.1、editor.toplevel

#### 2.2、WheelMove:SocialCalc.EditorProcessMouseWheel

##### 	2.2.1 判断editor此时状态，true则返回

##### 	2.2.2 sheet滚动处理

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

##### 	2.2.3 判断滚动方式为垂直还是水平，垂直则上下加减1，水平则左右加减1

```javascript
SocialCalc.ScrollRelative = function (editor, vertical, amount) {

    if (vertical) {
        editor.ScrollRelativeBoth(amount, 0);
    }
    else {
        editor.ScrollRelativeBoth(0, amount);
    }
    return;

}
```

##### 	2.2.4 调用ScrollRelativeBoth，处理横向、纵向滚动及渲染操作

```javascript
SocialCalc.ScrollRelativeBoth = function (editor, vamount, hamount) {
    var context = editor.context;

    // vplen为rowpanes参数个数，无冻结为1，冻结为2，数据中第一个为冻结参数，第二个为非冻结参数
    var vplen = context.rowpanes.length;
    // 处理向上滚动到顶的情况
    var vlimit = vplen > 1 ? context.rowpanes[vplen - 2].last + 1 : 1; // don't scroll past here 不要在这里滚动 行冻结
    if (context.rowpanes[vplen - 1].first + vamount < vlimit) { // limit amount 限制数量
        vamount = (-context.rowpanes[vplen - 1].first) + vlimit;
    }

    var hplen = context.colpanes.length;
    var hlimit = hplen > 1 ? context.colpanes[hplen - 2].last + 1 : 1; // don't scroll past here 不要在这里滚动 列冻结
    if (context.colpanes[hplen - 1].first + hamount < hlimit) { // limit amount 限制数量
        hamount = (-context.colpanes[hplen - 1].first) + hlimit;
    }

    if ((vamount == 1 || vamount == -1) && hamount == 0) { // special case quick scrolls 针对特别快速的滚动
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

    // 做一个总的滚动动作和渲染
    if (vamount != 0 || hamount != 0) {
        // 更新rowpanes、colpanes
        context.rowpanes[vplen - 1].first += vamount;
        context.rowpanes[vplen - 1].last += vamount;
        context.colpanes[hplen - 1].first += hamount;
        context.colpanes[hplen - 1].last += hamount;
        editor.FitToEditTable();
        editor.ScheduleRender();
    }
}
```

#### 2.3、editor

​	sheet编辑对象，存放参数、方法等