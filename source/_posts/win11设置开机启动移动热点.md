---
title: Win11设置开机启动移动热点
tags: []
id: '2487'
categories:
  - - 运维
date: 2022-12-31 16:54:34
---

全面开放后，赶紧从学校润回了家里，零刻SER5也带走了。家里的路由器无线功能不太行，准备用零刻SER5来分享移动热点，改善无线环境。

## 启动移动热点的脚本

```powershell
Add-Type -AssemblyName System.Runtime.WindowsRuntime
$asTaskGeneric = ([System.WindowsRuntimeSystemExtensions].GetMethods()  ? { $_.Name -eq 'AsTask' -and $_.GetParameters().Count -eq 1 -and $_.GetParameters()[0].ParameterType.Name -eq 'IAsyncOperation`1' })[0]
Function Await($WinRtTask, $ResultType) {
    $asTask = $asTaskGeneric.MakeGenericMethod($ResultType)
    $netTask = $asTask.Invoke($null, @($WinRtTask))
    $netTask.Wait(-1)  Out-Null
    $netTask.Result
}
Function AwaitAction($WinRtAction) {
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods()  ? { $_.Name -eq 'AsTask' -and $_.GetParameters().Count -eq 1 -and !$_.IsGenericMethod })[0]
    $netTask = $asTask.Invoke($null, @($WinRtAction))
    $netTask.Wait(-1)  Out-Null
}
 
$connectionProfile = [Windows.Networking.Connectivity.NetworkInformation,Windows.Networking.Connectivity,ContentType=WindowsRuntime]::GetInternetConnectionProfile()
$tetheringManager = [Windows.Networking.NetworkOperators.NetworkOperatorTetheringManager,Windows.Networking.NetworkOperators,ContentType=WindowsRuntime]::CreateFromConnectionProfile($connectionProfile)
if ($tetheringManager.TetheringOperationalState -eq 1) 
{
    "Hotspot is already On!"
}
else{
    "Hotspot is off! Turning it on"
    Await ($tetheringManager.StartTetheringAsync()) ([Windows.Networking.NetworkOperators.NetworkOperatorTetheringOperationResult])
}
```

```CMD
powershell.exe -command ^ "& {set-executionpolicy Remotesigned -Scope Process; .'"C:\D\wifi.ps1"' }"
```

*   第一个脚本写入 C:\\D\\wifi.ps1 文件
*   第二个脚本写入 C:\\D\\wifi.bat 文件
*   运行第二个脚本，确认可以启动移动热点（需要自己先手动配置一次移动热点）

## 计划任务配置系统启动自启

*   搜素 计划任务
*   创建任务
*   常规里设置 不管用户是否登录都要运行 使用最高权限运行
*   触发器里设置 在系统启动时
*   操作里设置 启动程序 C:\\D\\wifi.bat
*   条件里设置 只有在以下网络连接可用时才启动 任何连接
*   设置里设置 按下图进行设置
*   重启测试，确认运行正常

![](https://img.limour.top/archives_2023/2022/12/31/63aff8b266572.webp)