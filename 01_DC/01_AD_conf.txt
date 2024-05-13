DC configuration:

1. SConfig for:

    - static IP
      SET DNS TO IP OF AD
      Hyper-V custom internal adapter + external if needed, didn't work with default switch
      
    - rename
      well, maybe gove more creative name then just DC01...

2. ALternatively PowerShell for network configuration:
    
    Get-NetAdapter | Format-Table -AutoSize

    New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "192.168.37.1" -PrefixLength 24 -DefaultGateway "192.168.37.1"
    Set-NetIPInterface -InterfaceAlias "Ethernet0" -Dhcp Enabled

3. If server is not pingable:
    netsh advfirewall firewall add rule name="ICMP Allow" protocol=icmpv4:8,any dir=in action=allow # alternative: allow ping
    netsh advfirewall set allprofiles state off # disable firewall (DON'T DO THIS)

4. Enable WinRM
    Enable-PSRemoting
    TODO: add firewall rule for allowing WinRM

5. Install Domain Services:
    Get-WindowsFeature | ? {$_.Name -LIKE "AD*"}
    Install-WindowsFeature AD-Domain-Services -IncludeManagementTool



Win11 client:

1. Communicate from client to serer if both are not in the domain (e.g. strange error with winrm.cmd)
    Set-Item WSMan:\\localhost\client\trustedhosts -Value '192.168.37.2' -Concatenate -Force
    New-PSSession 192.168.37.2 -Credential (Get-Credential)
    Enter-PSSession 1 

2. Install chocolatey:
    # Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
    https://chocolatey.org/install

3. Chocolatey packages:
    https://community.chocolatey.org/packages
    - choco install git
    - choco install vscode