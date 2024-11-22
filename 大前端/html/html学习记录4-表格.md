## 表格table

表头 th

行　tr

列　td

***
```html
<table border="1px" width="600" cellpacing="0" cellpadding="4">
        <tr>
            <!--colspan跨列-->
            <td colspan="4">0-1</td>
        </tr>
        <tr>
            <!--rowspan　跨行-->
           <td rowspan="2">1-1</td>
           <td>1-2</td>
           <td>1-3</td>
           <td>1-4</td>
        </tr>    <table border="1px">
</table>
```

下面这些属性已经淘汰

属性border设置边界线宽度

属性width设置表格宽度

属性cellspacing设置两个单元格边界线之间的间隔

属性cellpadding设置单元格中的内容与边线的距离
