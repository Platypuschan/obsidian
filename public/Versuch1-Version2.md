# WSL2 network port forwarding script v1  
# for enable script, 'Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser' in Powershell,  
# for reset exist rules and ports use 'reset' as parameter, for show ports use 'list' as parameter.  
# written by Arne Kreusch, Okt-22-2021  
  
# You should modify '$Ports' for your applications  
$Ports = (2222,80,443,8000,9000)  
  
# Check WSL ip address  
$wslip = bash.exe -c "ip -o -4 -f inet addr show eth0 | cut -d '/' -f1 | cut -f7 -d ' '";  
if (-not $wslip) {  
echo "WSL ip not found. Terminate script.";  
read-host “Press ENTER to exit...” ;  
exit;  
}  
  
# Display all portproxy information  
If ($Args[0] -eq "list") {  
netsh interface portproxy show v4tov4;  
read-host “Press ENTER to exit...” ;  
exit;  
}  
  
# Reset all portproxy information  
If ($Args[0] -eq "reset") {  
Start-Process -Verb RunAs cmd.exe -Args '/c', "netsh int portproxy reset all";  
read-host “Press ENTER to exit...”  
exit;  
}  
  
# Add each port into portproxy  
$Addr = "0.0.0.0"  
Foreach ($Port in $Ports) {  
iex "netsh interface portproxy delete v4tov4 listenaddress=$Addr listenport=$Port | Out-Null";  
if ($Args[0] -ne "delete") {  
iex "netsh interface portproxy add v4tov4 listenaddress=$Addr listenport=$Port connectaddress=$WSL connectport=$Port | Out-Null";  
}  
}