# H5+JS+CSS3实现 七夕言情
[源自慕课网Aaron艾伦老师的教程](http://www.imooc.com/learn/453)

学到的知识：
* 如何实现页面的无缝滚动
* 如何实现视觉差效果
* 异步编程处理
* CSS3动画过渡
* JS动画实现
* H5的音乐效果

## 课程笔记

页面的横向布局

效果：
	页面主题会滚动         
	小男孩会走动         
通过二者的速率变化使用户产生视觉上的错觉（视觉差）         

整体布局结构：         
	横向页面         
	包含3个主题页面         
	页面之间无缝拼接         
	页面能滚动         

解决方案：         
做3个块级元素，每一个块元素代表一个主题页面的容器节点。设置父容器是3个块元素的宽，通过float处理，形成一个横向的3个无缝拼接的页面         

<ul id=”content-wrap”>         
<li>页面1</li>         
<li>页面2</li>         
<li>页面3</li>         
</ul>         
这样虽然书形成看横向布局与无缝拼接，不过可视区范围永远只能显示出一个主题页面的尺寸，所以我们需要再给祖先节点上套一个限制显示区域的节点
是指<dic id=”content”></div>         

在实现上，通过CSS布局是可以直接处理的。本课程中做了自适应页面布局处理，为了更精确页面的尺寸，这里采用JavaScript动态计算绝对的尺寸         

页面之间的卷滚切换效果         

实现页面与页面之间的切换：         
	移动父容器，改变父容器的坐标         
	移动每一个子容器的坐标         

改变坐标的处理：         
	传统的top，left坐标修改         
	CSS3中的transform属性         
第一种是修改style的坐标属性，，一般需要配合定时器使用         
第二种：         
transform属性向元素应用2D或3D转换，该属性允许我们对元素进行旋转、缩放、移动或倾斜，其中会有一个值translate3D(x, y, z)是用来控制元素的位置在页面上的三轴的位置，translate3D指明3D就是启用3d加速，也就是启动GPU来处理，性能更强。
transform属性是静态属性，不是动画属性，一旦写到style里面，	将会直接显示作用，无任何变化过程         

封装         

隐藏对象的属性和实现细节，仅对外公开接口         
JavaScript中没有抽象类与接口的支持，所以很大程度上实现封装都是靠闭包去模拟         

布局的自适应动态调整         

小男孩的布局与页面根节点应属于并列结构         

	小男孩只会在当前页面移动，所以是相对父级#content的范围         
	因为小男孩是合成的雪碧图，通过坐标取图，因此不能用CSS处理自适应布局，需要动态调整         
	采用动画的元素需要设置绝对定位         

人物坐标算法：         
小男孩的top坐标值=中间路段的中间坐标值-小男孩的高度         

雪碧图一般情况下是无法自适应缩放的         

精灵动画的实现         

定义一个animation，通过关键帧keyframe定义规则：         
	animation用于定于一些动画参数，比如执行的时间、方式、算法、次数等等         
	@keyframes规则用于创建动画，在@keyframes中规定某项CSS样式，就能创建由当前样式逐渐改为新样式的动画效果         
@keyframes用百分比来规定变化发生的时间，0%是动画的开始，100%是动画的完成         

运动状态控制         

animation：提供一个animation-play-state的样式控制动画的暂停处理         
transition：一般来说要暂停的地方都是一开始程序设定好的目标点，因此不需要控制，也没办法控制，这个是一个动画过渡的效果，浏览器只提供了一个动画结束的回调。但可以通过强制改变目标过渡值的处理进行控制，存在缺陷，下一次的启动必须等上一次动画的时间结束，不建议。

异步编程的处理         

传统的异步操作会在操作完成之后，使用回调函数传回结果，而回调函数中则包含了后续的工作。         

var dtd = $.Deferred();		// 创建         
dtd.resolve();					// 成功         
dtd.then();						// 执行回调         

页面与人物之间的视觉差效果         

注意：         
	小男孩的走路区间只是一个页面单位，相对点是父级的div         
	页面滚动有两个页面单位，因为本身会占据一个         
注意以上两点主要是为了设置比例的问题：                  
	小男孩如果走到中间位置，那么比例是0.5即0.5 * 页面宽度         
	页面要到中间位置的比例是1即1 * 页面宽度         

当小男孩走完第一段路的时候调用页面接口让页面开始滚动，之后通过boy.walkTo继续让小男孩走路我们只需要给小男孩与页面设置同样的位置与时间，调用scrollTop(5000, 1)传递时间与页面比例，页面就会自动滚动到指定的区域。

开门关门效果的实现

通过一个父元素定位，左右门可以采用left处理分别是0%、50%，通过js改变left值实现开、关门的效果。         
在实现的时候需要注意：         
	开关门是两组动画，需要两组transition的实现         
	需要监听两个动画是否完成         
	需要通过Deferred改善代码         

在监听动画完成上采用一个count计数，count开始为2，当执行第一个回调后会减1，只有当第二个回调执行时这个deferred.resolve才会执行         
var complete = function () {         
if (count == 1) {         
defer.resolve();         
return;         
}         
count--;         
]         

人物进入商店的坐标1计算

小男孩与商店在布局上是没有关系的，只是都是相对的靠近中间位置动作，那么随着分辨率的变化，二者之间的位置都有不同的变化，这里都是采用JS动态计算的。

小男孩走进门的中间位置具体的算法：         
translateX = 门中间的left值-小男孩中间的left值         
translateY = 人物底部的top值 - 门中间的top值         
这里的取值采用jQuery得offset处理，注意position与offset的取值点不同：offset()是相对于文档（document）的当前位置，position()是相对于父级元素的位移，一个元素可以嵌套多个position。

在实际的进门路线的处理中，代码是采用translateX + scale的组合，并没有采用translateY，原因是scale是一个缩放效果，在实际上会有替代translateY这个Y轴变化的感觉。

translate同样能改变页面的坐标。所以如果使用了translate的元素最终的坐标需要加上这个属性的值：         
元素的页面X坐标 = left + translateX         
元素的页面Y坐标 = top + translateY         

星星与水波

transition 与 animate 的不同：         
	transition需要事件触发         
	animate可以自动触发，功能更加强大，包括可以设置不同时间段的动画规则，还有状态的控制、事件等         

运动的轨迹处理

三段路的计算都是以小女孩为参考点，小男孩的最终坐标计算：         
top = girl.getPosition().top         
left = girl.getPosition().left         
第一段路：2秒钟走到页面left = 15%的位置         
第二段路：1.5秒走到页面left = 25%的位置，top就是小女孩的top位置         
第三段路：1.5秒走到小女孩的面前         

飘花效果的实现

实现原理：         
通过定时器调用JS代码不断的动态创建雪花节点，随机选择一个图片作为其背景，赋予三个初始的样式属性top，left与opacity，通过transition动画过渡的方式执行这3个属性的动画变化。
执行的流程：         
	getImageName随机6张图片，snowflakeURI定义一个地址的范围         
	creatSnowBox创建雪花元素的节点，并且增加一个snowRoll的样式，也就是旋转的关键帧实现         
	定时器设置200ms不断的1生成雪花对象，计算出3个属性的初始值，通过createSnowBox创建雪花元素，并且附上初始值，然后执行transition附上最终值，执行动画         
	动画结束后执行$(this).remove()删除这个对象         

总结
	自适应分辨率的问题可以采用的是JS + 百分比布局处理         
	通过合成“雪碧图”解决图片加载与资源占用的问题         
	通过CSS的animation实现帧动画并且控制状态         
	布局除了left，top布局外，还可以使用最新的css3的transition处理         
	元素的变化可以通过设置translate3D启动GPU加速         
	可以用CSS3的transition做渐变动画         
	HTML5音频的使用         
	采用promise可以解决异步编程的逻辑嵌套问题         
	代码组织的一些思路         
Swipe.js分析：         
1.	传入两个参数，第一个参数是显示区对象，第二个参数是文档内容对象         
	对象：element 获取第一个显示区对象（宽度为所有直接子节点宽度值和）         
	对象：swipe         
	对象：slides 获取第一个显示区的直接子节点         
	变量：width 获取显示区宽度         
	变量：height 获取显示区高度         
	变量：isComplete 监听动画是否完成         
	变量：timer 定时器，每500ms检测动画完成标记，若未完成执行页面移动动画         
	对象：callbacks 回调函数集？？         
	函数：monitorOffset 移动页面         
	属性：swipe.watch  在回调函数集中添加回调函数         
	属性：swipe.scrollTo 移动滚动条到指定位置         


Qixi.js分析：         
1 定义一个全局函数Qixi         
	定义一个对象confi         
	布尔值：keepZoomRadio                    
若为true         
	proportionY 一个缩放比例？         
	screenHeight 文档高度         
	zoomHeight 文档高度乘以缩放比例         
	zoomTop 文档高度与视口高度差的一半，即使视口在文档中居中         
1.	修改布局位置坐标         
2.	修改布局高度         
	对象：layer 用于确定布局位置         
	对象：audio 用于音频设置         
1.	如果启用音频，音频执行完毕后执行回调函数（即重复播放另一个音频，未实现！）         
	数组：snowflake 飘花图片         
	定义一个变量debug并赋初始值为0（当值为1时可用于调试）         
	定义一个变量instanceX（对象实例的x坐标值？）         
	定义一个变量container（用于表示显示区）         
1.	设置css：布局位置         
	定义一个变量visualWidth（用于表示显示区的宽度）         
	定义一个变量visualHeight（用于表示显示区的高度）         
	定义一个函数getValue（参数为类名，获取该类的高度和宽度）         
	定义一个函数pathY（获取第一个界面.a_background_middle的中间y坐标）         
	定义一个函数bridgeY（获取第三个界面.c_background_middle的y坐标）         
	定义一个函数animationEnd并且执行（返回浏览器类型）         
	定义一个对象swipe         
	重新定义一个函数scrollTo（参数为一个时间值和一个缩放比例，通过计算显示区比例下的大小调用swipe.scrollTo方法）         
	定义一个对象girl         
	变量：elem 获取女孩节点，方便后续操作         
	方法：getHeight 获取女孩高度         
	方法：rotate 为该节点添加一个girl-rotate类，通过CSS实现动画效果         
	方法：setOffset 设置该节点样式：x坐标为显示区的宽度值中点，y坐标为桥的y坐标减去女孩的高度值         
	方法：getOffset 获取该节点的offset         
	方法：getWidth 获取该节点的宽度         
	定义一个对象bird         
	变量：elem 获取鸟节点         
	方法：fly 为该节点添加动画样式         
	定义一个对象logo         
	变量：elem 获取logo节点         
	方法：run 为该节点添加动画样式         
	定义一个BoyWalk对象boy         
1.	男孩行走到显示区3/5的位置         
2.	第一张页面开始滚动到第二张页面，返回男孩走到显示区中间位置         
3.	飞鸟动画执行         
4.	男孩停止走路，返回男孩离开商店         
5.	设置女孩位置，第二张页面滚动到第三张页面，返回男孩走到桥下         
6.	返回男孩上桥         
7.	返回男孩直行走到女孩前面         
8.	男孩恢复成初始状态，女孩转身，男孩转身完成后出现logo和飘花动画开始         
	定义一个函数BoyWalk         
	对象：boy 获取男孩节点         
	变量：boyWidth 获取节点宽度         
	变量：boyHeight 获取节点高度         
	函数：pauseWalk 暂停走路动画（添加pauseWalk类）         
	函数：restoreWalk 移除走路动画（移除pauseWalk类）         
	函数：slowWalk 执行走路动画（添加slowWalk类）         
	函数：startRun 开始执行动画（返回一个延迟对象）         
	函数：walkRun 执行走路动画（返回一个延迟对象）         
	函数：walkToShop 进入商店动画（设置一个变量记录x坐标变化，设置缩放比值使男孩逐渐变小实现走进商店效果）         
	函数：walkOutShop 离开商店动画（男孩对象逐渐恢复到原大小实现走出商店效果）         
	函数：calculateDist 计算x坐标乘以缩放比值之后的大小         
1.	返回一个对象         
a)	walkTo 走到目标点         
b)	stopWalk 停止走路         
c)	resetOriginal 恢复初始状态         
d)	toShop 进入商店         
e)	outShop 离开商店         
f)	rotate 转身         
g)	getWidth 获取男孩宽度         
h)	getDistance 获取男孩x坐标（对象与文档左上角相对位置坐标）         
i)	talkFlow 添加拿花走路的动画的类         
	定义一个延迟对象BoyToShop         
	对象：defer         
	对象：$door 获取门节点对象         
	对象：doorLeft 获取左边的门节点对象         
	对象：doorRight 获取右边的门节点对象         
	函数：doorAction 设置门的动画，当开门关门两个动画都执行时完成         
	函数：openDoor 设置开门动画         
	函数：shutDoor 设置关门动画         
	函数：talkFlower 设置延迟，即男孩进店后出店前之间的事件         
	对象：lamp 设置灯节点对象的开灯和关灯属性         
	对象：waitOpen 设置男孩进店后出店前之间的事件         
1.	开灯事件         
2.	拿花事件         
3.	出店事件         
4.	关门事件         
关灯事件         
	定义一个函数snowflake         
	对象：$flakeContainer 获取飘花节点对象         
	函数：getImagesName 获取飘花图片（随机值）         
	函数：createSnowBox 返回飘花对象样式添加飘花动画         
1.	设置定时器，产生飘花                  
a)	变量：strartPositionLeft 设置初始位置x坐标的随机值         
b)	变量：starOpacity 设置初始状态透明度         
c)	变量：endPositionTop 设置结束位置y坐标         
d)	变量：endPositionLeft 设置结束位置x坐标         
e)	变量：duration 设置一个动画周期         
f)	变量：randomStart  设置初始状态透明度修正值         
g)	对象：$flake 设置飘花对象样式插入到飘花节点中且实现飘花动画         
	定义一个函数Html5Audio（设置音频）         
2 执行全局函数Qixi         
