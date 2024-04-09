---


---

<h2 id="error-aadsts700016--when--acquiring--token--for--azure--bot">Error AADSTS700016  When  Acquiring  Token  for  Azure  Bot</h2>
<h3 id="problem">Problem</h3>
<p>When  creating  an  App  in  Microsoft  Entra  ID  (Azure  AD)  “App  registrations”  with  “Supported  account  types”  set  to  “Accounts  in  this  organizational  directory  only  (your  organization  only  -  Single  tenant)”  and  configuring  its  “Certificates  &amp;  secrets,”  followed  by  creating  an  Azure  Bot  and  setting  the  “Microsoft  App  ID”  with  “Type  of  App”  as  “Single  Tenant”  to  correspond  to  the  aforementioned  App,  an  AADSTS700016  error  occurs  when  attempting  to  acquire  a  token  using  the  App’s  clientId  and  secrets  through  the  following  URL:</p>
<pre><code>https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token
</code></pre>
<p>The  error  message  states:</p>
<blockquote>
<p>“AADSTS700016: Application with identifier ‘c4c9c950-2042-1234-b487-3a1234567890’ was not found in the directory ‘Bot Framework’. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You may have sent your authentication request to the wrong tenant.”</p>
</blockquote>
<h3 id="solution">Solution</h3>
<p>According  to  the  documentation  “Request  an  access  token  from  the  Microsoft  Entra  ID  account  login  service,”  in  order  to  successfully  acquire  a  token  using  the  URL  <a href="https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token">https://login.microsoftonline.com/botframework.com/oauth2/v2.0/token</a>,  both  the  App’s  “Supported  account  types”  and  the  “Type  of  App”  for  the  Microsoft  App  ID  in  the  Azure  Bot  need  to  be  configured  as  “Multi  Tenant.”</p>
<p>If  they  are  set  to  “Single  Tenant,”  the  URL  for  acquiring  the  token  needs  to  be  modified  to:</p>
<pre><code>https://login.microsoftonline.com/&lt;your_TENANT_ID&gt;/oauth2/v2.0/token
</code></pre>
<p>By  implementing  this  change,  the  issue  should  be  resolved.</p>
<h3 id="reference--resource">Reference  Resource</h3>
<ul>
<li><a href="https://learn.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-4.0&amp;tabs=multitenant#step-1-request-an-access-token-from-the-microsoft-entra-id-account-login-service">Request  an  access  token  from  the  Microsoft  Entra  ID  account  login  service</a></li>
</ul>

