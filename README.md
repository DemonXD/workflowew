# workflowew
Python based workflow api

使用python 实现一个复杂的工作流引擎 
转自: https://cnblogs.com/duck-and-duck/p/14436373.html
支持功能：

- [ ] 要支持会签节点(会签节点就是一个大节点，里面有很多审批人，当这个大节点里的所有人都审批通过后，才能进入下一个节点)
>![支持会签](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRXqspOvO40ibb2WiaFXHjmiat4SvfAv8lF9ia0hYgups3aRkZN8ibhd6crLQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  
>把节点分为两大类：简单节点（上图中长方形）和复杂节点（上图中圆形）。  
>用一棵树表示整个流程，其中叶子节点都是简单节点，简单节点都是叶子节点。  
>每个简单节点里都有且仅有有一个审批人。  
>复杂节点包含若干个子节点。  
>加入会签节点：会签节点激活后，所有的子节点都可以审批，当所有的子节点都审批完毕后，会签节点完成。
>加入串行节点：子节点只能从左到右依次进行审批，当最后一个子节点审批完成后，串行节点完成。
>所有的工作流最外层都是一个串行节点，该节点完成后代表整个工作流完成。  
>节点状态：
>- Ready：可以进行审批操作的简单节点是 Ready 状态。
>- Complete：已经审批完成的节点状态。 
>- Future：现在还没有走到的节点状态。
>- Waiting：只有复杂节点有该状态，表示在等待子节点审批。
>
>借助上述规则，一次带会签节点的工作流审批过程如下：
>![工作流1](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRo1LQnakVe7qciczNBl0wab7jTzT3aEIh0mBPxO9wpo17iciaVUic6FzTKQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  
>![工作流2](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRibje4EFEQQcvWdY86bSnib2L4FydErbhnKnWaDzicOhlwVncPzJUpeHaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 支持并行节点(并行节点是一个包含很多审批人的大节点，这个大节点里任何一个人审批通过，则该节点就完成)
>然后很快就加入了并行节点：并行节点是一个复杂节点，该节点激活时，任何一个子节点都可以进行审批，且任何一个子节点是完成状态时，该节点完成。
>加入新状态 Skip：当一个并行节点的子节点状态为非（Ready, Waiting）时，其它兄弟节点及其子节点的状态被置为 Skip。
>举个栗子：
>![添加并行节点](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRge6eCHEic9Imyiavjjk9ES88RJgKQJic3gAIkIL8YVhibAHsIAIl8Q93TQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 节点要支持嵌套，比如会签节点里有个并行节点，并行节点里又有个复杂节点，要可以嵌套任意层的那种。
>能无限扩展的树形结构可以支持任意复杂流程。
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYReH4qnaGhSicBvBiclQe5dhG5JldmjUiaWrWBTTPdarTh2icPEbEX0v7aKQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 支持条件节点  
>工作流附带一个表单，要根据表单的内容确定下一步进入哪个分支。
>经过几天的冥思苦想，我加入了条件节点：条件节点类似并行节点，只不过只有满足条件的子节点才能进入接下来的审批。
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRXCKzTqXksIKbzAA2zhMIAsiaIU9OrlsP7duV8KxudE2BJCD3o9O6tMQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 审批人多加两种类型，比如可以从表单中选择下一个审批人，还有根据发起人不同选择不同的审批人。
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRL0238T7tYwmR1oRzxMXfumCDlLicxhp5n6rvkianSTFJGswIwSppqcibA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)   
>简单节点分成了 3 类：
>- 第一种：审批人是写死的。
>- 第二种：审批人从表单中读取。
>- 第三种：根据发起人和一个映射函数，算出审批人。比如 get_主管("钱某") 得到钱某的主管 李某。

- [ ] 节点可以从前往后审批, 从后往前驳回
>首先实现了驳回到发起人的功能，相当于一切从头开始：只有 Ready 状态的节点有权利驳回。（就像只有 Ready 状态的节点有权利审批一样）
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRDoib5Mu42q4jy8qxHAry9EwM0dgmCMQdlOia8FyEweGb4kYeOr0pg1cw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 

- [ ] 驳回到上一个审批人
>驳回到上一个审批人其实是个很复杂的逻辑，因为工作流中的节点可以无限嵌套，所以如何确定上一个状态有哪些审批人并不简单。
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRxQibb5yYk557gXKuPDwSiaTmEfsJLC4yDOEwzzPhe3RB60icBkSE5qCTQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 驳回到任意节点

>不断的驳回上一级，直到 Ready 状态的节点包含要驳回到的节点为止。

- [ ] 在普通节点加一个时间限制，要是在规定时间内没完成就显示已超时。

>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRfhaJ1ibRibaok7yTYPup7XKRyibPpWW464vMQoccGUDmibsjqYo5ZKiaS1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

- [ ] 代理功能，比如有件事让你审批，但是你拿不准，那就转给拿得准的人审批。
>马上我发现这个需求跟以往有本质的不同，以往的工作流的节点关系一开始就是固定的，就是在发起流程之前确定的，但是现在要在审批过程中更改。
>无非是加了一些班，掉了一些头发，最终设计了如下方案：
> - 代理操作的本质是，新建一个并行节点作为本节点的父节点，再新建一个兄弟节点放代理人，这样自己和代理人都能审批通过。
> - 代理操作可以无限嵌套，即代理人也可以找人代理。
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRhzCmYlvco2thJh78iaVcCDxoXHMRNuI0gqGxYhkjB5OJdLZ983Y4jqA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  
- [ ] 取消代理
>取消代理是代理的逆操作
>如果代理人审批过了那就不能取消代理
>![](https://mmbiz.qpic.cn/mmbiz_png/MOwlO0INfQrYZgDVVOK0vegQhiam2KLYRBaplK0GkNI21djRkKZdPKbC8UiaeAP2cD45WJiajQrpT0jOGJh9HSZ5Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1) 

- [ ] 给每个节点加个前后置条件吧，满足前置条件才能进入该节点，满足后置条件该节点才能审批完成。


- [ ] 工作流计指标：直观的显示目前审批进行的百分比。
>工作流完成的百分比指的是树中最右侧 Ready 状态的节点到最左侧节点的距离 / 最右侧节点的距离。

- [ ] 给每个节点挂两个可以执行的脚本，分别在开始审批该节点和审批完成该节点后执行
