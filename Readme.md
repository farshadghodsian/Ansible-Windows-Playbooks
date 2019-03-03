# SETTING UP ANSIBLE FOR WINDOWS SERVERS


##### Ansible Documentation on Windows Remote management  
https://docs.ansible.com/ansible/2.6/user_guide/windows_winrm.html#certificate  

##### For setting up Ansible to communicate with Windows Servers via WinRM pywinrm needs to be installed on anisble server  
<code>pip install pywinrm  </code>

##### For Dev use only to enable WinRM communication without any  over HTTP listener  
<code>winrm quickconfig  </code><br />
<code>winrm set winrm/config/service/auth '@{Basic="true"}'  </code><br />
<code>winrm set winrm/config/service '@{AllowUnencrypted="true"}'    </code>
  
##### For proper WinRM communication over HTTPS using selfsigncert download this PowerShell scrip from Ansible GITHUB Repo  
https://github.com/ansible/ansible/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1  

##### Then in PowerShell with Elevated Privileges:  
<code>.\ConfigureRemotingForAnsible.ps1 -ForceNewSSLCert  </code>

##### To see WinRM listeners running:  
<code>winrm enumerate winrm/config/listeners  </code>
  
##### Shutdown windows server (for reboots use win_reboot module). Also this is only for Dev use, Never shutdown a server outside DEV:  
<code>ansible -i hosts windows -m raw -a "Stop-Computer -Force" --ask-pass  </code>

##### Example Ad-Hoc command to show diskspace on windows server  
<code>ansible -i hosts -m raw -a "Get-PSDrive C,D" windows --ask-pass  </code>

##### Get Certificate Thumbprint via Powershell  
<code>Get-ChildItem -path cert:\LocalMachine\My  </code>

##### Setting WinRM HTTPS listener manually with specific cert thumbprint  
<code>$selector_set = @{  </code><br />
<code>&nbsp;&nbsp;&nbsp;&nbsp;Address = "*"  </code><br />
<code>&nbsp;&nbsp;&nbsp;&nbsp;Transport = "HTTPS"  </code><br />
<code>}  </code><br />
<code>$value_set = @{  </code><br />
<code>&nbsp;&nbsp;&nbsp;&nbsp;CertificateThumbprint = "?33C42A60F6FDD08707F851625097163D1C14C0C8"  </code><br />
<code>}</code><br /><br />  

<code> New-WSManInstance -ResourceURI "winrm/config/Listener" -SelectorSet $selector_set -ValueSet $value_set -UseSSL  </code>  
  
##### Set up WinRM Listener on a specific IP eg. management IP only  
<code>New-WSManInstance winrm/config/Listener -SelectorSet @{Address="*";Transport="HTTPS"} -ValueSet @{CertificateThumbprint = "??33C42A60F6FDD08707F851625097163D1C14C0C8"}  </code>

##### Remove all WinRM Listeners  
<code>Remove-Item -Path WSMan:\localhost\Listener\* -Recurse -Force  </code>

##### Only remove WinRM listeners that are run over HTTPS  
<code>Get-ChildItem -Path WSMan:\localhost\Listener | Where-Object { $_.Keys -contains "Transport=HTTPS" } | Remove-Item -Recurse -Force  </code>

##### Disable AllowUnencrypted  
<code>winrm set winrm/config/service '@{AllowUnencrypted="false"}'  </code><br />
<code>winrm set winrm/config/client '@{AllowUnencrypted="false"}'  </code>

##### Get overal WinRM Config  
<code>winrm get winrm/config  </code>

##### Convert .PFX cert to .pem cert (for private key) on Linux  
<code>openssl pkcs12 -in file.pfx -out file.pem -nodes  </code>
