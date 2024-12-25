# CRISvsGAME

## HTTP to WSL

### PowerShell

```
$Name = "Allow_Inbound_HTTP"
$DisplayName = "Allow Inbound HTTP"
$Action = "Allow"
$Direction = "Inbound"
$Enabled = "True"
$LocalPort = "80"
$Protocol = "TCP"

$NetFirewallRule = Get-NetFirewallRule -Name $Name -ErrorAction SilentlyContinue

if ($NetFirewallRule) {
    $NetFirewallPortFilter = $NetFirewallRule | Get-NetFirewallPortFilter

    $Updated = $False

    if ($NetFirewallRule.DisplayName -ne $DisplayName) {
        Set-NetFirewallRule -Name $Name -DisplayName $DisplayName
        Write-Host "Updated DisplayName to '$DisplayName'."
        $Updated = $True
    }

    if ($NetFirewallRule.Action -ne $Action) {
        Set-NetFirewallRule -Name $Name -Action $Action
        Write-Host "Updated Action to '$Action'."
        $Updated = $True
    }

    if ($NetFirewallRule.Direction -ne $Direction) {
        Set-NetFirewallRule -Name $Name -Direction $Direction
        Write-Host "Updated Direction to '$Direction'."
        $Updated = $True
    }

    if ($NetFirewallRule.Enabled -ne $Enabled) {
        Set-NetFirewallRule -Name $Name -Enabled $Enabled
        Write-Host "Updated Enabled to '$Enabled'."
        $Updated = $True
    }

    if ($NetFirewallPortFilter.LocalPort -ne $LocalPort) {
        Set-NetFirewallRule -Name $Name -LocalPort $LocalPort
        Write-Host "Updated LocalPort to '$LocalPort'."
        $Updated = $True
    }

    if ($NetFirewallPortFilter.Protocol -ne $Protocol) {
        Set-NetFirewallRule -Name $Name -Protocol $Protocol
        Write-Host "Updated Protocol to '$Protocol'."
        $Updated = $True
    }

    if (-not $Updated) {
        Write-Host "Firewall Rule '$Name' is configured correctly."
    }
} else {
    Write-Host "Firewall Rule '$Name' is not found. Creating new rule..."

    New-NetFirewallRule -Name $Name -DisplayName $DisplayName `
    -Action $Action -Direction $Direction -Enabled $Enabled `
    -LocalPort $LocalPort -Protocol $Protocol

    Write-Host "Firewall Rule '$Name' is created successfully."
}

$ListenAddress = "0.0.0.0"
$ListenPort = "80"
$ConnectAddress = (wsl hostname -I).Split(" ")[0].Trim()
$ConnectPort = "80"

Write-Host "PortProxy Rule 'HTTP to WSL'. Creating new rule..."

netsh interface portproxy add v4tov4 listenaddress=$ListenAddress `
listenport=$ListenPort connectaddress=$ConnectAddress connectport=$ConnectPort

Write-Host "PortProxy Rule 'HTTP to WSL' is created successfully."
```
