# CRISvsGAME

## HTTP to WSL

### Windows PowerShell

```
New-NetFirewallRule -DisplayName 'HTTP Inbound' -Protocol TCP -LocalPort 80
netsh interface portproxy add v4tov4 `
listenport=80 `
listenaddress=0.0.0.0 `
connectport=80 `
connectaddress=(wsl hostname -I).Split(' ')[0].Trim()
```
