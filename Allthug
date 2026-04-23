# ==========================================
#   FiveM Full Performance Script
#   รวมทุกค่า: GPU + CPU + Network + Services
#   Run as Administrator!
# ==========================================

Write-Host "===========================================" -ForegroundColor Cyan
Write-Host "   FiveM Full Performance Tweaks" -ForegroundColor Cyan
Write-Host "===========================================" -ForegroundColor Cyan
Write-Host ""

# ฟังก์ชันช่วย Set Registry
function SetReg($path, $name, $value, $type = "DWord") {
    if (!(Test-Path $path)) {
        New-Item -Path $path -Force | Out-Null
    }
    Set-ItemProperty -Path $path -Name $name -Value $value -Type $type -Force
}

# ==========================================
#   1. GPU Driver Stability
# ==========================================
Write-Host "[1/6] GPU Driver..." -ForegroundColor Yellow

$gpuPath = "HKLM:\SYSTEM\CurrentControlSet\Control\GraphicsDrivers"
SetReg $gpuPath "TdrLevel"   0
SetReg $gpuPath "TdrDelay"   60
SetReg $gpuPath "HwSchMode"  2

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   2. CPU Priority
# ==========================================
Write-Host "[2/6] CPU Priority..." -ForegroundColor Yellow

$sysProfile = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile"
SetReg $sysProfile "SystemResponsiveness"    0
SetReg $sysProfile "NetworkThrottlingIndex"  ([uint32]"0xffffffff")

$gamePath = "$sysProfile\Tasks\Games"
SetReg $gamePath "GPU Priority"        8
SetReg $gamePath "Priority"            6
SetReg $gamePath "Scheduling Category" "High"    "String"
SetReg $gamePath "SFIO Priority"       "High"    "String"
SetReg $gamePath "Background Only"     "False"   "String"
SetReg $gamePath "Clock Rate"          10000

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   3. Timer Resolution
# ==========================================
Write-Host "[3/6] Timer Resolution..." -ForegroundColor Yellow

$timerPath = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NtSetTimerResolution"
SetReg $timerPath "DefaultResolution" 10000
SetReg $timerPath "MaximumResolution" 10000
SetReg $timerPath "MinimumResolution" 10000

$kernelPath = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\kernel"
SetReg $kernelPath "GlobalTimerResolutionRequests" 1

$priorityPath = "HKLM:\SYSTEM\CurrentControlSet\Control\PriorityControl"
SetReg $priorityPath "Win32PrioritySeparation" 0x26
SetReg $priorityPath "IRQ8Priority"            1
SetReg $priorityPath "IRQ16Priority"           2

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   4. Network / TCP
# ==========================================
Write-Host "[4/6] Network Tweaks..." -ForegroundColor Yellow

$tcpPath = "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters"
SetReg $tcpPath "TCPNoDelay"              1
SetReg $tcpPath "TcpAckFrequency"         1
SetReg $tcpPath "TCPDelAckTicks"          0
SetReg $tcpPath "TcpDelayedAckTimeout"    0
SetReg $tcpPath "TcpDelayedAckFrequency"  1
SetReg $tcpPath "DefaultTTL"             64
SetReg $tcpPath "MaxUserPort"            65534
SetReg $tcpPath "TcpTimedWaitDelay"      30
SetReg $tcpPath "ECNCapability"          1
SetReg $tcpPath "EnableCTCP"             1
SetReg $tcpPath "FastSendDatagramThreshold" 1500
SetReg $tcpPath "SackOpts"               1

# QoS ปลดล็อก Bandwidth
$qosPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\Psched"
SetReg $qosPath "NonBestEffortLimit" 0

# DNS Cache
$dnsPath = "HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters"
SetReg $dnsPath "CacheHashTableBucketSize" 1
SetReg $dnsPath "CacheHashTableSize"       384
SetReg $dnsPath "MaxCacheEntryTtlLimit"    64000
SetReg $dnsPath "MaxSOACacheEntryTtlLimit" 301

# NDIS RSS
$ndisPath = "HKLM:\SYSTEM\CurrentControlSet\Services\Ndis\Parameters"
SetReg $ndisPath "RssBaseCpu"     0
SetReg $ndisPath "MaxNumRssCpus"  4

# ปิด IPv6 (ถ้าไม่ใช้)
SetReg "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" "DisabledComponents" 0xff

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   5. Desktop / UI
# ==========================================
Write-Host "[5/6] Desktop Settings..." -ForegroundColor Yellow

$desktopPath = "HKCU:\Control Panel\Desktop"
SetReg $desktopPath "ForegroundLockTimeout" 0
SetReg $desktopPath "ForegroundFlashCount"  0
SetReg $desktopPath "MenuShowDelay"         "0" "String"

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   6. ปิด Services ที่ไม่จำเป็น
# ==========================================
Write-Host "[6/6] Disabling Services..." -ForegroundColor Yellow

$services = @(
    "WSearch",        # Windows Search
    "SysMain",        # Superfetch
    "wuauserv",       # Windows Update
    "DoSvc",          # Delivery Optimization
    "Spooler",        # Print Spooler
    "Fax",            # Fax
    "RemoteRegistry", # Remote Registry
    "bthserv",        # Bluetooth
    "XblAuthManager", # Xbox
    "XblGameSave",    # Xbox
    "XboxNetApiSvc",  # Xbox
    "XboxGipSvc",     # Xbox
    "DiagTrack",      # Telemetry
    "dmwappushservice",# Telemetry
    "MapsBroker",     # Maps
    "lfsvc",          # Geolocation
    "WerSvc",         # Error Reporting
    "RetailDemo",     # Retail Demo
    "WpcMonSvc"       # Parental Controls
)

foreach ($svc in $services) {
    try {
        $regSvcPath = "HKLM:\SYSTEM\CurrentControlSet\Services\$svc"
        if (Test-Path $regSvcPath) {
            SetReg $regSvcPath "Start" 4
            Write-Host "      Disabled: $svc" -ForegroundColor DarkGray
        }
    } catch {
        Write-Host "      Skip: $svc" -ForegroundColor DarkGray
    }
}

Write-Host "      OK" -ForegroundColor Green

# ==========================================
#   เสร็จแล้ว!
# ==========================================
Write-Host ""
Write-Host "===========================================" -ForegroundColor Cyan
Write-Host "   เสร็จแล้ว! กรุณา Restart PC" -ForegroundColor Green
Write-Host "===========================================" -ForegroundColor Cyan
Write-Host ""

$restart = Read-Host "Restart ตอนนี้เลยไหม? (y/n)"
if ($restart -eq "y") {
    Restart-Computer -Force
}
