---
title: SocialCalc即时渲染
data:  2018-01-30 09:09:01
tag:
- SocialCalc
categories: 前端
---

SocialCalc即时渲染简介

**作者**：[DanBricklin](https://github.com/DanBricklin)

**源码地址：** [https://github.com/DanBricklin/socialcalc](https://link.jianshu.com/?t=http://jsfiddle.net/audreyt/LtDyP/)

**使用技术：** JavaScript+HTML+CSS

**主界面截图:**

![SocialCalc主页面](http://upload-images.jianshu.io/upload_images/10390285-ca9e2adf0d08f588.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
        初始化grid
        创建grid
    
        SocialCalc.CreateTableEditor = function (editor, width, height) {}

一、初始化参数

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

二、适配grid，设置页面显示行数、列数

    editor.FitToEditTable();

三、渲染grid

    editor.EditorRenderSheet();

四、添加grid节点

    editor.griddiv.appendChild(editor.fullgrid);

五、纵向、横向滚动条配置

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

六、table配置及渲染

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

七、纵向、横向滚动条渲染

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

八、添加table节点

    editor.toplevel.appendChild(editor.layouttable);

九、添加滚动条监听事件，实现滚动即时渲染

    // sheet滚动操作 重点*****
    SocialCalc.MouseWheelRegister(editor.toplevel, {WheelMove:     
    SocialCalc.EditorProcessMouseWheel, editor: editor});

事件说明
    https://www.jianshu.com/p/e7fa2cddd4af