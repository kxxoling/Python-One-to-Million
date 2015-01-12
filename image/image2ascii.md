# 图片转字符原理
图片转字符通常分以下几种：

1. 黑白算法
2. 灰度算法
3. 边际追踪／界定算法

## 黑白算法

黑白算法最简单

低保真度解法：

    ##### ####   ## #####
      #   #     #     #
      #   ##    #     #
      #   #      #    #
      #   #### ##     #

高保真度解法：

    88888 8888  d8b 88888
      8   8    ]b     8
      8   88    Yb    8
      8   8    o d8   8
      8   8888  YP    8

低保真度解法仅仅简单地通过字符 `#` 和空白符表示图形区域地颜色，
高保真算法则使用 12 个字符对应一个 `2*2` 的区域，对照表如下：

<table border="1">
<tbody><tr>
<td>Pattern:</td>
<td><pre data-initialized="true">..
..</pre></td>
<td><pre data-initialized="true">.X
..</pre></td>
<td><pre data-initialized="true">X.
..</pre></td>
<td><pre data-initialized="true">..
X.</pre></td>
<td><pre data-initialized="true">..
.X</pre></td>
<td><pre data-initialized="true">XX
..</pre></td>
<td><pre data-initialized="true">..
XX</pre></td>
<td><pre data-initialized="true">X.
X.</pre></td>
<td><pre data-initialized="true">.X
.X</pre></td>
<td><pre data-initialized="true">.X
XX</pre></td>
<td><pre data-initialized="true">XX
.X</pre></td>
<td><pre data-initialized="true">XX
X.</pre></td>
<td><pre data-initialized="true">XX
XX</pre></td></tr><tr></tr>
<tr> <td>Character:</td>
 <td><pre data-initialized="true">`</pre></td>
 <td><pre data-initialized="true">'</pre></td>
 <td><pre data-initialized="true">.</pre></td>
 <td><pre data-initialized="true">,</pre></td>
 <td><pre data-initialized="true">"</pre></td>
 <td><pre data-initialized="true">_</pre></td>
 <td><pre data-initialized="true">(</pre></td>
 <td><pre data-initialized="true">)</pre></td>
 <td><pre data-initialized="true">J</pre></td>
 <td><pre data-initialized="true">L</pre></td>
 <td><pre data-initialized="true">7</pre></td>
 <td><pre data-initialized="true">P</pre></td>
 <td><pre data-initialized="true">8</pre></td>
</tr>
</tbody></table>


## 灰度算法
灰度算法的基本目标是追求转换后的每个字符亮度都尽可能接近该区域原始图像的亮度，
因此在文字渐渐缩小或者与人眼的距离越来越远时，看起来越发接近图片。
边缘、边框以及其它高对比度的地方应该使用更加契合其结构的字符。

灰度算法需要使用实现亮度－字符的转换，大多数具体实现都采用 1 个像素对 1 个字符的转换，
“state of the art”算法使用 4 个像素对应一个字符，并且能够提供更清晰的结果。


## 边际追踪／界定算法
边际界定算法看起更加复杂，但其实不然！


全文主要翻译自 [java.de](http://www.jave.de/image2ascii/algorithms.html)
