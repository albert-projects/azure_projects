---


---

<h2 id="on-premises--web--app--accessing--azure--key--vault--via--rbac">On-premises  Web  App  Accessing  Azure  Key  Vault  via  RBAC</h2>
<h3 id="introduction">Introduction</h3>
<p>We  have  registered  an  on-premises  Web  App  (myapp)  in  Azure  AD,  which  automatically  creates  an  associated  Azure  service  principal  also  named  “myapp.”  This  article  explores  the  configuration  necessary  for  “myapp”  to  access  secrets  stored  within  an  Azure  Key  Vault.</p>
<h3 id="implementation">Implementation</h3>
<p>The  configuration  and  coding  approach  can  follow  the  guidelines  outlined  in  the  tutorial  “Tutorial:  Use  a  managed  identity  to  connect  Key  Vault  to  an  Azure  web  app  in  .NET”.  However,  since  the  system  resides  on-premises,  we  need  to  replace  DefaultAzureCredential  with  ClientSecretCredential.</p>
<p>ClientSecretCredential  requires  tenantId,  clientId,  and  clientSecret.  To  obtain  these  values:</p>
<ol>
<li>
<p>Navigate  to  the  “Manage”  section  of  your  “myapp”  service  principal.</p>
</li>
<li>
<p>Select  “Certificates  &amp;  secrets.”</p>
</li>
<li>
<p>Create  a  new  Client  Secret  and  retrieve  its  value.</p>
</li>
</ol>
<p><strong>Reference  Resource:</strong></p>
<ul>
<li><a href="https://learn.microsoft.com/en-us/azure/key-vault/general/tutorial-net-create-vault-azure-web-app">Tutorial:  Use  a  managed  identity  to  connect  Key  Vault  to  an  Azure  web  app  in  .NET</a></li>
</ul>

