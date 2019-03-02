################################################
#### SETTING UP ANSIBLE FOR WINDOWS SERVERS ####
################################################

#For setting up Ansible to communicate with Windows Servers via WinRM pywinrm needs to be installed on anisble server
pip install pywinrm


#For Dev use only to enable WinRM communication without any  over HTTP listener
winrm quickconfig
winrm set winrm/config/service/auth '@{Basic="true"}'
winrm set winrm/config/service '@{AllowUnencrypted="true"}''='


#For proper WinRM communication over HTTPS using selfsigncert
#Download this PowerShell scrip from Ansible GITHUB Repo
https://github.com/ansible/ansible/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1

#Then in PowerShell with Elevated Privileges:
.\ConfigureRemotingForAnsible.ps1 -ForceNewSSLCert

#To see WinRM listeners running in powershell type:
winrm enumerate winrm/config/listeners

#Shutdown windows server (for reboots use win_reboot module). Also this is only for Dev use, Never shutdown a server outside DEV:
ansible -i hosts windows -m raw -a "Stop-Computer -Force" --ask-pass

#Example Ad-Hoc command to show diskspace on windows server
ansible -i hosts -m raw -a "Get-PSDrive C,D" windows --ask-pass

#Setting Environment variables from a file eg. to clear proxy settings if you are using proxy on ansible server but cant communicate with your local dev windows servers
export $(grep -v '^#' /etc/ansible/noproxy | xargs)

