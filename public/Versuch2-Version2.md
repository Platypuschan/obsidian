$wslip = bash.exe -c "ip -o -4 -f inet addr show eth0 | cut -d '/' -f1 | cut -f7 -d ' '";
$command = "netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=2222 connectaddress=$wslip connectport=2222" ;
echo  "$wslip" ;
Start-Process -Verb RunAs cmd.exe -Args '/c', $command

