---


---

<h2 id="migrating--vmdk--vhd--to--azure">Migrating  VMDK  (VHD)  to  Azure</h2>
<p>This  article  is  the  notes  for  migrating  a  VMWare  VM  (vmdk)  to  Azure.<br>
Ensure  the system have  downloaded  and  installed  the  following  software:</p>
<ul>
<li>
<p><strong>Microsoft  Virtual  Machine  Converter  3.0:</strong>  This  tool  utilizes  PowerShell  cmdlets  to  convert  VMDK  to  VHD.  After  downloading,  extract  and  place  it  in  the  C:\Program  Files  directory.</p>
</li>
<li>
<p><strong>AzCopy:</strong>  This  tool  facilitates  uploading  the  VHD  to  Azure  Disk  (it  can  reside  in  the  same  directory  as  the  vmdk).</p>
</li>
<li>
<p><strong>Azure  CLI:</strong>  This  tool  will  be  used  to  create  the  Azure  Disk  and  the  Azure  VM.</p>
</li>
</ul>
<h3 id="implementation">Implementation:</h3>
<p><strong>1.  Converting  VMDK  to  VHD</strong></p>
<ul>
<li>
<p>I  will  utilize  Microsoft  Virtual  Machine  Converter  3.0  to  convert  the  VMDK  to  VHD  format.</p>
</li>
<li>
<p>Download  and  extract  Microsoft  Virtual  Machine  Converter  3.0  to  the  C:\Program  Files  directory.  The  resulting  directory  will  be  named  Microsoft  Virtual  Machine  Converter.</p>
</li>
<li>
<p>Open  PowerShell  as  an  administrator  and  execute  the  following  command  (adjust  SourceLiteralPath  and  DestinationLiteralPath  according  to  the  location  of  your  vmdk  file)  to  initiate  the  conversion:</p>
</li>
</ul>
<pre><code>Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath "C:\vm\win2012.vmdk" -DestinationLiteralPath "C:\vm\win2012.vhd" -VhdType FixedHardDisk -VhdFormat Vhd
</code></pre>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Migrating_VMDK/vmdk01.png" alt="VMDK1"></p>
<ul>
<li>In  this  example,  I  only  uploading  one  VM,  so  I   haven’t  use  Sysprep.</li>
</ul>
<p><strong>2.  Setting  the  VHD  File  to  a  Fixed  Size  (Integer  GB)</strong></p>
<ul>
<li>Open  PowerShell  as  an  administrator  and  resize  the  VHD  file  to  64GB:</li>
</ul>
<pre><code>Resize-VHD -Path c:\vm\win2012.vhd -SizeBytes 64GB
</code></pre>
<p><strong>3.  Creating  an  Empty  Azure  Disk  Using  Azure  CLI</strong></p>
<ul>
<li>
<p>Open  a  Command  Prompt  window.</p>
</li>
<li>
<p>Log  in  to  Azure:</p>
</li>
</ul>
<pre><code>az login
</code></pre>
<ul>
<li>Set  the  default  subscription:</li>
</ul>
<pre><code>az account set --subscription "Your Azure Subscription ID"
</code></pre>
<ul>
<li>Create  a  resource  group  (or  use  an  existing  one):</li>
</ul>
<pre><code>az group create --location eastus --name vwwareVMs
</code></pre>
<ul>
<li>Create  an  Azure  Disk  and  set  the  size  using  the  –upload-size-bytes  parameter:</li>
</ul>
<pre><code>az disk create -n vmwareDisk -g vwwareVMs -l eastus --for-upload --upload-size-bytes 68719477248 --sku standardssd_lrs --hyper-v-generation V1
</code></pre>
<ul>
<li>Note:  The  size  of  the  VHD  must  match  the  size  of  the  Azure  Disk.  Otherwise,  it  will  encounter  an  error  during  the  AzCopy  process.</li>
</ul>
<p><strong>4.  Obtaining  Writable  SAS  for  the  Azure  Disk  Using  Azure  CLI</strong></p>
<ul>
<li>Execute  the  following  command  to  obtain  a  writable  SAS  URL  for  the  Azure  Disk:</li>
</ul>
<pre><code>az disk grant-access -n vmwareDisk -g vwwareVMs --access-level Write --duration-in-seconds 86400
</code></pre>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Migrating_VMDK/vmdk02.png" alt="VMDK2"></p>
<ul>
<li>Note  the  accessSAS  value  from  the  output.</li>
</ul>
<p><strong>5.  Uploading  the  VHD  to  Azure  Disk  Using  AZCopy</strong></p>
<ul>
<li>Use  the  AzCopy  tool  to  upload  the  VHD  to  the  Azure  Disk  using  the  SAS  URL:</li>
</ul>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Migrating_VMDK/vmdk03.png" alt="VMDK3"></p>
<pre><code>AzCopy.exe copy "C:\VM\win2012.vhd" "your_accessSAS_value" --blob-type PageBlob
</code></pre>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Migrating_VMDK/vmdk04.png" alt="VMDK"></p>
<ul>
<li>This  process  may  take  some  time  depending  on  the  size  of  the  VHD  and  your  internet  speed.</li>
</ul>
<p><strong>6.  Revoking  the  SAS  for  the  Azure  Disk</strong></p>
<ul>
<li>Once  the  upload  is  complete,  revoke  the  SAS  access  to  the  Azure  Disk:</li>
</ul>
<pre><code>az disk revoke-access -n vmwareDisk -g vwwareVMs
</code></pre>
<p><strong>7.  Creating  the  VM  and  Attaching  the  Uploaded  Disk</strong></p>
<ul>
<li>Create  a  new  VM  in  Azure  and  specify  the  uploaded  disk  as  the  OS  disk:</li>
</ul>
<pre><code>az vm create --resource-group vwwareVMs --location eastus --name vmwareVM1 --os-type windows --attach-os-disk vmwareDisk
</code></pre>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Migrating_VMDK/vmdk05.png" alt="VMDK5"><br>
Once  the  VM  creation  is  complete,  you  can  connect  to  it  via  Remote  Desktop  and  remove  VMware  Tools.  It  can  also  adjust  the  VM  size,  configure  automatic  shutdown,  and  other  settings  in  the  Azure  Portal.</p>
<p><strong>Complete  Script:</strong></p>
<pre><code>; Convert VMDK to VHD
Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath "C:\vm\win2012.vmdk" -DestinationLiteralPath "C:\vm\win2012.vhd" -VhdType FixedHardDisk -VhdFormat Vhd
Resize-VHD -Path c:\vm\win2012.vhd -SizeBytes 64GB

; Azure CLI commands
az login
az account set --subscription "Your Azure Subscription ID"
az group create --location eastus --name YourResourceGroupName
az disk create -n YourAzureDiskName -g YourResourceGroupName -l eastus --for-upload --upload-size-bytes VHDFileSize --sku standardssd_lrs --hyper-v-generation V1
az disk grant-access -n YourAzureDiskName -g YourResourceGroupName --access-level Write --duration-in-seconds 86400

; Upload VHD using AzCopy
AzCopy.exe copy "C:\VM\win2012.vhd" "accessSAS_value" --blob-type PageBlob

; Revoke SAS and create VM 
az disk revoke-access -n YourAzureDiskName -g YourResourceGroupName 
az vm create --resource-group YourResourceGroupName --location eastus --name NewVMName --os-type windows --attach-os-disk YourAzureDiskName
</code></pre>
<p><strong>Note:</strong>  If  the  above  steps  fail,  try  opening  the  converted  VHD  in  a  local  Hyper-V  environment  to  verify  its  functionality.  Remove  VMware  Tools  from  the  VM  and  then  follow  the  instructions  in  “Prepare  a  Windows  VHD  or  VHDX  to  upload  to  Azure”  to  configure  the  VM.</p>
<p><strong>References:</strong></p>
<ul>
<li>
<p><a href="https://learn.microsoft.com/en-us/azure/virtual-machines/windows/prepare-for-upload-vhd-image">Prepare  a  Windows  VHD  or  VHDX  to  upload  to  Azure</a></p>
</li>
<li>
<p><a href="https://learn.microsoft.com/en-us/azure/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli">Upload  a  VHD  to  Azure  or  copy  a  managed  disk  to  another  region  -  Azure  CLI</a></p>
</li>
<li>
<p><a href="https://www.sourceonetechnology.com/convert-vmware-vmdk-files-into-hyper-v-vhd/">How  to  Convert  VMware  VMDK  Files  Into  Hyper-V  VHD  Files</a></p>
</li>
<li>
<p><a href="https://learn.microsoft.com/en-us/powershell/module/hyper-v/resize-vhd?view=windowsserver2019-ps">Resize-VHD</a></p>
</li>
<li>
<p><a href="https://azure.microsoft.com/en-us/pricing/details/managed-disks/">Managed Disks pricing</a></p>
</li>
</ul>

