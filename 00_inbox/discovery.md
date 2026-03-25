- discovery
* `aws iam list-users`
* `aws iam list-roles`
* `aws iam list-policies`
* `aws configure [--profile $<profile>]`
* `aws sts get-caller-identity --profile $<profile>`
* `aws sts assume-role --role-arn $<role_arn> --role-session-name $<session> --profile $<profile>`
* `aws iam list-users --profile $<profile>`
* `aws iam list-roles --profile $<profile>`
* `aws iam list-policies --scope Local --profile $<profile>`
* `aws iam list-access-keys --user-name $<user> --profile $<profile>`
* `aws sts get-session-token --duration-seconds $<seconds> --profile $<profile>`
* `aws sts get-caller-identity`
* `aws ec2 describe-images`
*   `aws ec2 describe-snapshots`
*   `az storage blob list`
*   `az vm image list`
*   `gsutil ls`
*   `gcloud compute images list`
*   `curl -s $<target_url> | grep -oP '$<strings>-\w{8}'`
*   `aws s3 ls --profile $<user>`
*   `aws s3 ls $<bucket> --profile $<user>`
*   `aws ec2 describe-images --owners amazon --executable-users all --profile $<profile>`
*   `aws ec2 describe-images --executable-users all --filters "Name=description,Values=*$<keyword>*" --profile $<profile>`
*   `aws ec2 describe-images --executable-users all --filters "Name=name,Values=*$<keyword>*" --profile $<profile>`
*   `aws ec2 describe-snapshots --filters "Name=description,Values=*$<keyword>*" --profile $<profile>`
*   `curl -I http://$<bucket>.s3.amazonaws.com/`
*   `curl -I https://$<name>.blob.core.windows.net/`
*   `curl -I https://$<name>.azurewebsites.net/`
*   `curl -I https://storage.googleapis.com/$<bucket>/`
*   `curl -I http://$<project>.appspot.com/`
*   `cloud_enum -k $<keyword> --quickscan --disable-azure --disable-gcp`
*   `cloud_enum -kf $<keyfile> --quickscan --disable-azure --disable-gcp`
*   `for key in $<words>; do echo "$<prefix>-$key-$<suffix>"; done | tee $<keyfile>`
*   `aws ec2 describe-instances`
*   `aws s3 ls`
*   `aws route53 list-hosted-zones`
*   `dig +short NS $<domain>`
*   `host $<domain>`
*   `amass enum -d $<domain>`
*   `subfinder -d $<domain>`
*   `aws configure`
*   `aws ec2 describe-instances --region $<region> --profile $<profile>`
*   `aws s3 mb s3://$<bucket_name> [--profile $<profile>]`
*   `aws s3api put-bucket-policy --bucket $<bucket_name> --policy file://$<policy>.json --profile $<profile>`
*   `aws iam create-role --role-name $<role_name> --assume-role-policy-document file://$<trust>.json --profile $<profile>`
*   `aws iam update-assume-role-policy --role-name $<role_name> --policy-document file://$<trust>.json --profile $<profile>`
*   `aws iam put-user-policy --user-name $<name> --policy-name s3-read --policy-document file://$<policy-name>.json --profile $<profile>`
*   `aws iam list-groups-for-user --user-name $<user> --profile $<profile>`
*   `aws iam list-user-policies --user-name $<user> --profile $<profile>`
*   `aws iam list-attached-user-policies --user-name $<user> --profile $<profile>`
*   `aws iam list-attached-group-policies --group-name $<group> --profile $<profile>`
*   `aws iam list-policy-versions --policy-arn $<arn> --profile $<profile>`
*   `aws iam get-policy-version --policy-arn $<arn> --version-id $<version> --profile $<profile>`
*   `aws iam get-account-authorization-details --filter User Group LocalManagedPolicy Role --profile $<profile> | tee account-authorization-details.json`
*   `aws iam get-account-summary | tee account-summary.json`
*   `aws iam get-account-authorization-details --filter User Group LocalManagedPolicy Role | tee account-authorization-details.json`
*   `aws --profile $<profile> ec2 describe-instances --region $<region>`
*   `aws --profile $<profile> s3 ls`
*   `aws --profile $<profile> rds describe-db-instances --region $<region>`
*   `az account show`
*   `az group list`
*   `az vm list`
*   `az vm image list --all`
*   `az storage account list`
*   `az storage blob list --account-name $<name> --container-name $<container>`
*   `az network dns zone list`
*   `az network dns record-set list -z $<zone>`
*   `az account show --subscription $<sub>`
*   `az vm list --resource-group $<rg>`
*   `gcloud projects list`
*   `gcloud config list`
*   `gcloud compute instances list`
*   `gcloud compute images list`
*   `gsutil ls`
*   `gsutil ls gs://$<bucket>`
*   `gcloud dns managed-zones list`
*   `gcloud dns record-sets list --zone=$<zone>`
*   `gcloud compute instances list --project $<project>`
*   `aws s3 sync s3://$<bucket> ./static_content`
*   `aws s3 sync s3://$<bucket> $<local_dir> --profile $<profile>`
*   `systeminfo`
*   `hostname`
*   `wmic os get Caption,Version`
*   `wmic os get Caption, Version, OSArchitecture`
*   `Get-ComputerInfo`
*   `uname -a`
*   `lsb_release -a`
*   `hostnamectl`
*   `cat /etc/os-release`
*   `df -h`
*   `lsblk`
*   `free -m`
*   `top`
*   `vmstat`
*   `cat /etc/fstab`
*   `env`
*   `tzutil /g`
*   `powershell Get-WinSystemLocale`
*   `powershell [System.Globalization.CultureInfo]::InstalledUICulture`
*   `powershell Get-Culture`
*   `powershell [System.Globalization.CultureInfo]::CurrentCulture.Name`
*   `powershell [System.Globalization.CultureInfo]::InstalledUICulture.Name`
*   `date`
*   `timedatectl`
*   `locale`
*   `echo $LANG`
*   `cat /etc/locale.conf`
*   `curl ipinfo.io`
*   `curl ifconfig.co/json`
*   `whoami`
*   `echo %USERNAME%`
*   `query user`
*   `[System.Security.Principal.WindowsIdentity]::GetCurrent().Name`
*   `quser`
*   `qwinsta`
*   `id`
*   `id -un`
*   `who`
*   `last`
*   `w`
*   `echo $USER`
*   `net user`
*   `dir c:\users`
*   `dsquery user`
*   `Get-ADUser -Filter *`
*   `net localgroup Administrators`
*   `Get-MsolUser`
*   `cat /etc/passwd`
*   `ls -la /home`
*   `groups $<username>`
*   `Get-LocalUser`
*   `az ad user list`
*   `aws iam list-users`
*   `whoami /groups`
*   `net localgroup`
*   `net localgroup $<group>`
*   `net group /domain`
*   `net group "$<group_name>" /domain`
*   `net user /domain`
*   `net view /domain`
*   `Get-ADGroupMember -Identity $<group>`
*   `dsquery user -name $<strings>`
*   `nltest /dclist:$<domain>`
*   `getent group $<group>`
*   `ldapsearch -x -H ldap://$<DC> -b "dc=example,dc=com" "(objectClass=person)"`
*   `ldapsearch -x -b "dc=example,dc=com" "(samAccountName=$<name>)"`
*   `dsquery group -name $<group>`
*   `ldapsearch -x -b "CN=$<group>,OU=Groups,DC=example,DC=com"`
*   `Get-MsolRole`
*   `az ad user get-member-groups --id $<user>`
*   `aws iam list-groups`
*   `gcloud identity groups list`
*   `net accounts`
*   `dir $<path> /s /b`
*   `Get-ChildItem -Path $<path> -Recurse | Select-Object Name, FullName, Length, LastWriteTime`
*   `Get-ChildItem -Recurse -Filter "*.pdf"`
*   `where /r $<path> $<filename>`
*   `ls -la $<path>`
*   `find $<path> -type f -name "$<filename>"`
*   `find /home -name "*.pdf"`
*   `find / -writable -type d 2>/dev/null`
*   `find / -perm -u=s -type f 2>/dev/null`
*   `find / -perm 4000 -type f 2>/dev/null`
*   `locate *.key`
*   `stat $<file>`
*   `locate $<filename>`
*   `mount | grep -E "cifs|nfs"`
*   `smbclient -L //$<host> -U $<user>`
*   `ls -1 /usr/share/nmap/scripts/smb*`
*   `nmap -v -p 139,445 --script smb-os-discovery $<target>`
*   `tasklist`
*   `tasklist /v`
*   `Get-Process`
*   `Get-WmiObject Win32_Process`
*   `ps -ef`
*   `ps -aux`
*   `ps aux | grep defender`
*   `ps aux --sort=start_time`
*   `ps -auxfww`
*   `cat /proc/$<pid>/status`
*   `top -b -n 1`
*   `htop`
*   `./pspy64 --interval 1`
*   `netstat -ano`
*   `netstat -tulnp`
*   `ss -tulnp`
*   `ipconfig /all`
*   `arp -a`
*   `route print`
*   `netsh interface ip show config`
*   `nbtstat -a`
*   `netstat -anop tcp`
*   `Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings"`
*   `ifconfig -interface`
*   `netstat -tuln`
*   `ip route show`
*   `netstat -rn`
*   `routel`
*   `ip address show`
*   `route -n`
*   `nmcli dev show`
*   `cat /etc/iptables/rules.v4`
*   `cat /etc/hosts`
*   `route $print`
*   `Get-DnsClientServerAddress -AddressFamily IPv4`
*   `reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings"`
*   `netsh winhttp show proxy`
*   `ip route show default`
*   `cat /etc/resolv.conf`
*   `echo $http_proxy`
*   `echo $https_proxy`
*   `cat ~/.curlrc`
*   `cat ~/.wgetrc`
*   `netsh wlan show profiles`
*   `netsh wlan show interfaces`
*   `netsh wlan show profile name=$<SSID> key=clear`
*   `nmcli dev wifi list`
*   `iwlist $interface scan`
*   `iwconfig $interface`
*   `reg query $<registry_path>`
*   `reg query HKLM\Software`
*   `Get-ItemProperty -Path $<registry_path>`
*   `Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"`
*   `wmic /namespace:\\root\default path StdRegProv`
*   `gsettings get $<schema> $<key>`
*   `dconf read $<path>`
*   `sc query $`
*   `sc query state=all`
*   `tasklist /svc`
*   `net start`
*   `Get-Service -Name $`
*   `Get-WmiObject -Class Win32_Service -Filter "Name='$"`
*   `systemctl --type=service`
*   `service --status-all`
*   `chkconfig --list`
*   `initctl list`
*   `sc query $<ServiceName>`
*   `Get-Service`
*   `Get-Service -Name $<ServiceName>`
*   `systemctl status $<ServiceName>.service`
*   `systemctl list-units --type=service`
*   `nltest /domain_trusts`
*   `Get-ADTrust -Filter *`
*   `netdom trust $<domain> /domain:$<domain> /verify`
*   `ldapsearch -x -H ldap://$<domain_controller> -b "CN=Configuration,DC=$<domain>,DC=local"`
*   `rpcclient -U "" $<target_ip> -c "enumdomtrusts"`
*   `enum4linux -a $<target_ip>`
*   `gpresult /r`
*   `gpresult /scope computer /v`
*   `gpresult /H $<output>.html`
*   `Get-GPO -All`
*   `Get-GPOReport -All -ReportType XML`
*   `Get-GPOReport -All -ReportType HTML -Path "C:\report.html"`
*   `Get-GPOReport -Name $<GPO_name> -ReportType HTML -Path $<OutFile>`
*   `Get-GPPermission -Name $<GPO_name> -All`
*   `Get-DomainGPO`
*   `Get-DomainGPOLocalGroup`
*   `smbclient //$<DC>/SYSVOL -U "$<Domain>\\$<User>"`
*   `net view $<target>`
*   `ping -n 1 <target>`
*   `1..254 | ForEach-Object { if (Test-Connection -ComputerName "$<target_ip_3>.$_" -Count 1 -Quiet) { "Host $<target_ip_3>.$_ is up" } }`
*   `for /L %i in (1,1,254) do @ping -n 1 -w 100 $<target_ip_3>.%i | find "TTL=" && echo $<target_ip_3>.%i is up`
*   `Get-NetNeighbor -AddressFamily IPv4`
*   `Get-ADComputer -Filter * -Property Name`
*   `nmap -sn $<subnet>/24`
*   `for i in {1..254} ;do (ping $<target_ip>.$i -c 1 -W 5 >/dev/null && echo "$<target_ip_3>.$i" &) ;done`
*   `arp-scan --localnet`
*   `netdiscover -r $<subnet>/24`
*   `ip neigh`
*   `arp -n`
*   `ss [-punt] [-a] [-l]`
*   `ss -puntl`
*   `ss -punta`
*   `ss -tuln`
*   `ss -tulwn`
*   `lsof [-i :$<num>]`
*   `nmap [-sT] $<string>`
*   `nmap $<target_or_range> $<flags>`
*   `1..1024 | % {try{$c=New-Object Net.Sockets.TcpClient;$a=$c.BeginConnect("$<host_IP>",$_,$null,$null);if($a.AsyncWaitHandle.WaitOne(300,$false)){"Port $_ open"};$c.Close()}catch{}}`
*   `1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("$<host_IP>", $_)) "TCP port $_ is open"} 2>$null`
*   `Test-NetConnection -ComputerName $<target> -Port $<port>`
*   `Get-NetTCPConnection | Where-Object { $_.RemoteAddress -eq $<target> }`
*   `portqry.exe $<target> $<port>`
*   `nmap -sS -sV -p- $<target_ip_or_range>`
*   `nmap -p- $<target>`
*   `for p in 22 80 443; do timeout 1 bash -c "echo >/dev/tcp/$<target_ip>/$p" && echo "Port $p open"; done`
*   `for p in {1..100}; do timeout 0.5 bash -c "echo >/dev/tcp/$<target_ip>/$p" && echo "Port $p open"; done`
*   `masscan $<target_or_range> -p$<ports> --rate $<pps>`
*   `avahi-browse -a`
*   `dns-sd -B _ssh._tcp .`
*   `nxc $<protocol> $<target_ip>/$<range>`
*   `nxc $<protocol> $<target_ip> -u $<user_name> -p $<password>`
*   `echo public > community; echo private >> community; echo manager >> community`
*   `for ip in $(seq 1 254); do echo 192.168.50.$ip; done > ips`
*   `onesixtyone -c community -i ips`
*   `ftp $<target>`
*   `smbclient -L //$<target>/ -N`
*   `rdpscan $<target>`
*   `showmount -e $<target>`
*   `ssh $<username>@$<IP>`
*   `hydra -L users.txt -P passwords.txt ssh://$<target>`
*   `hydra -l anonymous -p "" ftp://$<target>`
*   `snmpwalk -v2c -c $<community> $<target_ip>`
*   `snmpwalk -v3 -u $<user> -l authPriv -a SHA -A $<auth_pass> -x AES -X $<priv_pass> $<target_ip>`
*   `snmpwalk -v{1|2c|3} -c $<community> [-t $<timeout>] $<target> $<OID>`
*   `snmpwalk -c public -v1 [-t 10] $<target> $<OID_MIB>`
*   `snmpget -v2c -c $<community> $<target_ip> $<OID>`
*   `snmpbulkwalk -v2c -c $<community> $<target_ip>`
*   `show running-config`
*   `net view $<target_host>`
*   `Invoke-Command -ComputerName $<target_host> -ScriptBlock { Get-SmbShare }`
*   `Invoke-ShareFinder -Verbose`
*   `net use`
*   `wmic logicaldisk get DeviceID,ProviderName,DriveType,FileSystem,VolumeName`
*   `mountvol`
*   `Get-PSDrive -PSProvider FileSystem`
*   `Get-SmbMapping`
*   `Get-Volume`
*   `Get-WmiObject Win32_LogicalDisk | Select-Object DeviceID, ProviderName, DriveType, FileSystem, VolumeName`
*   `smbclient -L $<target_ip> -U guest`
*   `smbmap -H $<target_host> -u $<username> -p $<password>`
*   `nmap -p 445 --script smb-enum-shares $<target_host>`
*   `Import-Module .\PowerView.ps1`
*   `Get-NetDomain`
*   `Get-NetComputer`
*   `Get-NetComputer | select operatingsystem,dnshostname,objectsid`
*   `Get-NetUser`
*   `Get-NetUser | select cn`
*   `Get-NetUser | select cn,pwdlastset,lastlogon`
*   `Get-NetUser -SPN | select samaccountname,serviceprincipalname`
*   `Get-NetGroup | select cn`
*   `Get-NetGroup "Sales Department" | select member`
*   `Find-DomainShare`
*   `setspn -L iis_service`
*   `SharpHound.exe -c All`
*   `SharpHound.exe -c ObjectProps,Group,Session`
*   `Invoke-BloodHound -CollectionMethod All -Domain CONTOSO -ZipFileName out.zip`
*   `Get-Help Invoke-BloodHound`
*   `bloodhound-python -u $<user> -p $<pass> -d $<domain> -ns $<dc> --zip`
*   `SharpHound.exe -c All -d $<domain> -u $<username> -p $<password> -domaincontroller $<dc_ip> -o $<output_folder>`
*   `wmic product get name,version`
*   `Get-WmiObject -Class Win32_Product | Select-Object Name, Version`
*   `reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall /s`
*   `wmic product get name`
*   `dpkg -l`
*   `rpm -qa`
*   `find / -type f -name "*chrome*" 2>/dev/null`
*   `ps aux | grep $<software_name>`
*   `ps -aux | grep -i av`
*   `systemctl list-units | grep -i av`
*   `chkconfig --list`
*   `Add-Type -AssemblyName System.Windows.Forms; [System.Windows.Forms.Form]::ActiveForm.Text`
*   `xdotool getactivewindow getwindowname`
*   `Get-Process | Select-Object ProcessName, MainWindowTitle, Id`
*   `wmctrl -l`
*   `Get-AzResource -ResourceGroupName $<group>`
*   `Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/me"`
*   `az resource list`
*   `az storage account list`
*   `az vm list`
*   `gcloud compute instances list`
*   `gcloud projects list`
*   `Get-WmiObject -Query "SELECT * FROM Win32_USBHub"`
*   `Get-PnpDevice`
*   `BluetoothFindFirstDevice`
*   `lsusb`
*   `lspci`
*   `cat /proc/bus/input/devices`
*   `lsblk`
*   `blkid`
*   `udevadm monitor`
*   `net time \\$<hostname>`
*   `w32tm /tz`
*   `Get-Date`
*   `GetTickCount()`
*   `uptime`
*   `time()`
*   `driverquery /v /fo csv`
*   `Get-WmiObject Win32_SystemDriver | Select-Object DisplayName, PathName, State`
*   `reg query HKLM\SYSTEM\CurrentControlSet\Services /s`
*   `lsmod`
*   `modinfo $<module_name>`
*   `cat /proc/modules`
*   `find /lib/modules/$(uname -r)/kernel -type f -name "*.ko"`
*   `wevtutil qe $<logname> /f:text /c:$<count>`
*   `Get-EventLog -LogName $<logname> -Newest $<count>`
*   `Get-WinEvent -LogName $<logname>`
*   `cat /var/log/$<logfile>`
*   `journalctl -u $<service>`
*   `ausearch -m $<event_type>`
*   `Get-VM`
*   `vmrun list`
*   `VBoxManage list vms`
*   `Get-WmiObject -Namespace root\virtualization\v2 -Class Msvm_ComputerSystem`
*   `virsh list --all`
*   `qemu-img info $<image_file>`
*   `esxcli vm process list`
*   `vim-cmd vmsvc/getallvms`
*   `nslookup $<domain>`
*   `whois $<domain>`
*   `Resolve-DnsName $<domain> -Type A`
*   `dig $<domain> [any]`
*   `dig $<domain> [axfr]`
*   `dig @<target_DNS> <target_domain> axfr`
*   `host -t any $<domain>`
*   `nmap -sP $<ip_range>`
*   `nmap -O $<target>`
*   `Invoke-WebRequest $<url>`
*   `Sublist3r -d $<domain>`
*   `subfinder -d $<domain> -o $<output>`
*   `amass enum -d $<domain> -o $<output>`
*   `dnsrecon -d $<domain> -t std`
*   `dnsenum $<domain>`
*   `gobuster dns -d $<domain -w wordlist.txt -t 10`
*   `nslookup -type=$<record_type> $<domain>`
*   `dig $<domain> $<record_type>`
*   `traceroute $<host>`
*   `nmap -p 389 $<host>`
*   `ldapsearch -x -H ldap://$<host>`
*   `Invoke-RestMethod http://ipinfo.io/json`
*   `curl ifconfig.me`
*   `nmap -sS -p 80,443 $<target>`
*   `Get-NetRoute`
*   `tcpdump -i $<interface>`
*   `iptables -L`
*   `rasdial`
*   `Get-VpnConnection`
*   `ps aux | grep openvpn`
*   `nmcli connection show`
*   `Invoke-UserHunter`
*   `ADFind -default -f "objectClass=user"`
*   `theHarvester -d $<domain> -b google`
*   `ldapsearch -x -LLL -H ldap://$<target_ip> -b "dc=example,dc=com" "(objectClass=user)"`
*   `Invoke-WebRequest -Uri "https://hunter.io/search/$<domain>" -OutFile hunter.html`
*   `theHarvester -d $<domain> -b bing`
*   `Invoke-WebRequest -Uri $<url> | Select-String -Pattern $<keyword>`
*   `curl https://www.linkedin.com/company/$<company>/people`
*   `theHarvester -d $<domain> -b $<source>`
*   `curl -s $<url> > $<filename>`
*   `recon-ng`
*   `marketplace install all`
*   `modules load recon/domains-hosts/bing_domain_web`
*   `run`
*   `Invoke-WebRequest -Uri $<company_location_url> -OutFile $<filename>`
*   `curl -s $<company_location_url> > $<filename>`
*   `Invoke-WebRequest -Uri $<partners_url> -OutFile $<filename>`
*   `curl -s $<partners_url> > $<filename>`
*   `Invoke-WebRequest -Uri $<calendar_url> -OutFile $<filename>`
*   `curl -s $<calendar_url> > $<filename>`
*   `Invoke-WebRequest -Uri $<leadership_url> -OutFile $<filename>`
*   `curl -s $<leadership_url> > $<filename>`
*   `curl $<domain>/robots.txt`
*   `python dirsearch.py -u $<url> -e $<ext> -w $<wordlist>`
*   `dirb $<url> $<wordlist>`
*   `gobuster dir -u $<url> -w $<wordlist> -t 50`
*   `wget $<url> -O $<filename>`
*   `wget -p -H -E -nH -k $<url>`
*   `rustscan -a $<target_ip> -- -sC -sV [--script-args http.useragent=random]`
*   `masscan $<target_ip> -p$<port_range> --rate $<rate>`
*   `sudo nmap -sS -sC -sV $<target_ip> [--script-args http.useragent=random] [-oN $<output_file>]`
*   `sudo nmap --top-ports 100 $<target_ip> [-oN $<output_file>]`
*   `sudo nmap -sU --script=dns-nsid -p53 $<target_ip> [-oN scan_result.txt]`
*   `sudo nmap -sC -sV -A -p $<port> $<target_ip> [-oN scan_result.txt]`
*   `nuclei -u $<target_ip:port> -t http/technologies/`
*   `nuclei -u $<target_ip:port> -tags php`
*   `nmap -p 80,443 --script http-waf-detect $<target_ip>`
*   `curl -vv -A "$<Agent_Name>" $<url>`
*   `nmap --script vuln $<target_ip>`
*   `nuclei -u $<target_ip:port> -t cves`
*   `nuclei -u $<target_ip:port> -t http`
*   `nuclei -u $<target_ip:port> -t network`
*   `whatweb -v $<target_url> [--user-agent "$<Agent_Name>"]`
*   `python-wappalyzer $<url>`
*   `nikto -h $<target_ip>`
*   `nc -zv $<target> $<start_port>-$<end_port>`
*   `nmap -sS -F [-T4] $<target>`
*   `nmap -vvv -p- $<target>`
*   `nmap -sS -sV sC -p $<num> [-T4] $<target>`
*   `nmap -sU --top-ports $<num> $<target>`
*   `ls /usr/share/nmap/scripts/ | grep $<service_type>`
*   `nmap -p21 --script=ftp-syst $<target_ip>`
*   `nmap -p21 --script=ftp-vsftpd-backdoor $<target_ip>`
*   `nmap -p21 --script=ftp-anon $<target_ip>`
*   `nmap -p21 --script=ftp-brute $<target_ip>`
*   `nmap --script=brute -p 22 $<target1_ip>`
*   `nmap -p 25 --script=smtp-commands.nse $<target_ip>`
*   `nc -vn $<target_ip> 25`
*   `swaks --to $<username>@$<target_FQDN> --from test@example.local --server $<target_ip> --data "Subject: Test\n\nThis is a test email."`
*   `wpscan --url $<target_url>`
*   `wpscan --url $<target_url> --enumerate u`
*   `hydra -l $<username> -P /usr/share/wordlists/seclists/Passwords/500-worst-passwords.txt $<Target_FQDN_or_IP> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Ftraining.local%3A8051%2Fwp-admin%2F&testcookie=1:Lost your password?" -e s -t 64`
*   `ffuf -u $<url>/FUZZ -H "User-Agent: $<Agent_Name>" -w $<wordlist>`
*   `feroxbuster -u $<URL> -w $<wordlist_path> -a "$<Agent_Name>"[ -x $<extensions>]`
*   `gobuster dir -u <URL> -w <wordlist_path> -a "$<Agent_Name>"`
*   `whatweb $<url>`
*   `wpscan --url $<url> --enumerate u,p,t`
*   `dirb $<URL> $<wordlist> -X $<extension>`
*   `sudo nmap -p80 --script=http-enum $<target_ip`
*   `curl -i $<url>/v1`
*   `curl -d '{"password":"$<password>","username":"$<username>"}' -H 'Content-Type: application/json' http://192.168.50.16:5002/users/v1/$<api_code>`
*   `ffuf -u $<url>/FUZZ -H "User-Agent: $<Agent-Name>" -w $<wordlist> -e $<extension>`
*   `nslookup -type=any $<domain>`
*   `openssl s_client -connect $<domain>:443`
*   `curl -I $<domain>`
*   `dig ANY $<domain>`
*   `shodan search $<ip>`
*   `curl https://crt.sh/?q=$<domain>`
*   `nmap -Pn $<ip>`
*   `Start-Process "C:\Tor Browser\Browser\firefox.exe"`
*   `Invoke-WebRequest -Uri "https://api.rocketreach.co/v1/search?query=$<keyword>" -Headers @{Authorization="Bearer $<token>"}`
*   `torify curl $<hidden_service_url>`
*   `modules load recon/contacts-contacts/rocketreach`
*   `set SOURCE $<domain>`
*   `Invoke-WebRequest -Uri "https://api.threatconnect.com/v2/indicators?owner=Public&filter=$<keyword>" -Headers @{Authorization="Bearer $<token>"}`
*   `curl -H "Authorization: Bearer $<token>" "https://api.recordedfuture.com/v2/ip/$<ip_address>/risk"`
*   `OnionScan --url $<hidden_service_url>`
*   `onionscan --url $<hidden_service_url>`
*   `Get-Mailbox`
*   `grep -r "email" $<user_home>`