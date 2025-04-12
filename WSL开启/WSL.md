
# Windows Hyper-V 和 WSL 设置说明

## 1. 启用 Hyper-V 和 WSL 所需的功能
要启用与 Hyper-V 和 WSL 相关的功能，运行以下命令：

### 启用 HypervisorPlatform
```bash
dism.exe /Online /Enable-Feature /FeatureName:HypervisorPlatform /All /LimitAccess
```

### 启用 VirtualMachinePlatform
```bash
dism.exe /Online /Enable-Feature /FeatureName:VirtualMachinePlatform /All /LimitAccess
```

### 启用 WSL 功能
```bash
dism.exe /Online /Enable-Feature /FeatureName:Microsoft-Windows-Subsystem-Linux /All /LimitAccess
```

## 2. 检查已启用的功能
要查看系统中已启用的功能，运行以下命令：

```bash
dism.exe /Online /Get-Features /Format:Table
```

## 3. 重启计算机
执行上述命令后，重启计算机以确保更改生效。

## 4. 检查虚拟化设置
确保 **虚拟化技术**（Intel VT-x 或 AMD-V）在 BIOS 设置中已启用。这对于 WSL 2 和 Hyper-V 至关重要。

## 5. 启动 WSL
重启后，可以通过运行以下命令测试 WSL 是否正常工作：

```bash
wsl
```

## 6. 解决挂起状态（如果适用）
如果某些功能（如 **VirtualMachinePlatform**）仍处于“挂起”状态，可以再次启用这些功能并重启计算机：

```bash
dism.exe /Online /Enable-Feature /FeatureName:VirtualMachinePlatform /All /LimitAccess
```

# 关闭hyper-v
```
dism.exe /Online /Disable-Feature /FeatureName:Microsoft-Hyper-V-All
```
