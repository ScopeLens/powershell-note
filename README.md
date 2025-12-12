# powershell-note
powershell命令与脚本笔记

## 核心概念

1. Cmdlet:最小命令单元  
   cmdlet(Command-let)是PowerShell的基础命令单元,执行之后会返回一到多个.net对象  
格式都是:`[动词]-[名词]`
2. 对象管道  
   `|`管道符号,在传统的bash中管道符号传递的是字符串,而在ps中传递的是对象
3. Provider和PSDrive:资源驱动器抽象
   ps提供了一种统一的资源访问方式:Provider模型,它把各种资源隐射为虚拟驱动器(PSDrive)
4. 脚本与模块
   脚本(.ps1):包含一系列命令,流程控制,函数等
   模块(.psm1/.psd1):可以复用的功能单元
5. 执行策略
   |策略|含义|
   |----|---|
   |Restricted|默认,不允许运行任何脚本|
   |RemoteSigned|允许本地脚本运行,远程脚本需签名|
   |AllSigned|所有脚本都需签名|
   |Bypass|无限制|
   
   设置执行策略`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

## 对象处理相关操作

|命令|作用说明|
|----|-------|
|Where-Object|条件筛选对象|
|Select-Object|选择或重命名对象属性|
|Sort-Object|根据某个属性对对象排序|
|Group-Object|按照属性对对象进行分组统计|
|Measure-Object|统计对象数量,总和,平均等|
|Export-Csv|将对象导出为CSV文件|
|ConvertTo-Json|将对象转换为JSON格式字符串|

筛选对象  
`Get-Service | Where-Object {$_.Status -eq 'Running'}`  
说明:  
`$_`表示当前管道中的对象  
Status是服务对象的一个属性

选择特定属性  
`Get-Process | Select-Object Name,Id,CPU`  
选择对象的一些属性导出  

添加计算列(计算列)  
就像是js中map遍历构建新的属性然后导出新的自定义属性  
`Get-Process|Select-Object Name, @{Name="MemoryMB";Expression={$_.WorkingSet / 1MB -as [int]}}`  
说明:  
`@{}`创建一个哈希表定义新属性  
`Name`指定新列名称  
`Expression`提供计算逻辑

排序  
对进程按CPU使用率排序  
`Get-Process | Sort-Object CPU -Descending`  

分组  
`Get-Service | Group-Object Status`  

导出  
导出为CSV文件  
`Get-Service | Select-Object Name,Status | Export-Csv -Path data.csv -NoTypeInformation`  

格式化  
转换成JSON格式  
`Get-Service | ConvertTo-Json`

## 基础命令

Get-Process  
语法:`Get-Process`  
作用:返回一个进程对象

Get-Member  
语法:`Get-Member`  
作用:去获取一个对象的描述  
实例:  
`Get-Process|Get-Member`将进程对象通过管道符输入到Get-Member命令中,可以查看进程对象的结构,包括各种属性以及相关类型名之类的信息  

## 自动化脚本