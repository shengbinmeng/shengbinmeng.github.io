---
title: Windows热点自动开启
date: 2026-02-09 00:00:00
category: 技术
tags: [Windows,Wi-Fi]
---

我在自己的Windows电脑上设置了热点，但它默认情况下不会随着电脑启动而自动开启，而我又容易忘记手动操作。
为解决此问题，我增加了一个脚本来开启热点，并让该脚本开机自动执行。

<!--more-->

脚本内容如下：

```ps
;@Findstr -bv ;@F "%~f0" | powershell -command - & goto:eof

Add-Type -AssemblyName System.Runtime.WindowsRuntime

$connectionProfile = [Windows.Networking.Connectivity.NetworkInformation,Windows.Networking.Connectivity,ContentType=WindowsRuntime]::GetInternetConnectionProfile()
$tetheringManager = [Windows.Networking.NetworkOperators.NetworkOperatorTetheringManager,Windows.Networking.NetworkOperators,ContentType=WindowsRuntime]::CreateFromConnectionProfile($connectionProfile) 

Function EnableHotspot {
    if ($tetheringManager.TetheringOperationalState -eq 1) {
        "Hotspot is already On!"
    } else {
        "Hotspot is off! Turning it on"
        $tetheringManager.StartTetheringAsync() | Out-Null
    }
}

Function DisableHotspot {
    if ($tetheringManager.TetheringOperationalState -eq 0) {
        "Hotspot is already Off!"
    } else {
        "Hotspot is on! Turning it off"
        $tetheringManager.StopTetheringAsync() | Out-Null
    }
}

EnableHotspot
```

这里面的大部分内容是Windows PowerShell的代码，但它其实是作为批处理文件（.bat）来保存和运行的。
其中第一行的意思是将该文件之后的字符串都作为参数传给`powershell -command`命令。
该命令即调用系统的powershell解释器来执行传给它的脚本代码。
之所以这样做，是因为Windows不支持开机直接运行PowerShell的脚本文件，或许觉得其过于强大（Power），容易有安全隐患吧。
批处理文件不受这一限制，但它又不便于实现热点的开启，所以最终做成了上面这种PowerShell代码嵌入到批处理文件中的形式。

为使上述批处理文件开机自动运行，将其放在`C:\Users\Shengbin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`文件夹下即可。
此外还能通过添加任务计划程序或者修改注册表来实现，不过感觉没那么便捷了。