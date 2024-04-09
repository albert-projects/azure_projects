---


---

<h2 id="configuring--additional--claims--for--an--oidc--app--in--azure">Configuring  Additional  Claims  for  an  OIDC  App  in  Azure</h2>
<h3 id="introduction">Introduction</h3>
<p>Recently,  a  colleague  encountered  a  situation  where,  when  connecting  to  Azure  OIDC  via  CAS,  the  Claims  received  by  CAS  from  Azure  only  included  the  default  set.  However,  we  needed  to  include  the  “mailnickname”  attribute  from  Azure  User  properties  as  an  additional  Claim  to  be  sent  to  CAS.  This  article  details  the  configuration  steps  to  achieve  this.</p>
<h3 id="procedure">Procedure</h3>
<p>Typically,  during  the  setup  process,  an  App  is  created  within  “App  registrations”  in  Microsoft  Entra  ID  (formerly  Azure  AD)  with  “Supported  account  types”  set  to  “Single  tenant”.  This  action  also  creates  a  corresponding  App  within  “Enterprise  applications”.</p>
<p><strong>1.  Configuring  “acceptMappedClaims”  in  the  App  Manifest</strong></p>
<ul>
<li>
<p>Within  “App  registrations,”  select  the  created  App  and  click  on  “Manifest.”</p>
</li>
<li>
<p>Set  the  value  of  “acceptMappedClaims”  to  “true.”</p>
</li>
<li>
<p>The  default  value  of  “accessTokenAcceptedVersion”  is  null,  which  corresponds  to  v1.0  id_tokens.  If  v2.0  is  required,  change  this  value  to  “2”.</p>
</li>
<li>
<p>For  detailed  configuration  information,  refer  to:  <a href="https://learn.microsoft.com/en-us/entra/identity-platform/reference-app-manifest?WT.mc_id=Portal-Microsoft_AAD_RegisteredApps">Microsoft  Entra  application  manifest</a></p>
</li>
</ul>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/OIDC_Add_Claims_SSO/oidc01.png" alt="OIDC"></p>
<p><strong>2.  Adding  Claims  in  “Single  sign-on”  for  the  App  within  "Enterprise  applications"</strong></p>
<ul>
<li>
<p>Navigate  to  “Enterprise  applications”  and  select  the  created  App.</p>
</li>
<li>
<p>Click  on  “Single  sign-on.”</p>
</li>
<li>
<p>In  section  2,  “Attributes  &amp;  Claims,”  click  on  “Edit.”</p>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/OIDC_Add_Claims_SSO/oidc02.png" alt="OIDC"></p>
</li>
<li>
<p>In  the  “Attributes  &amp;  Claims”  section,  click  on  “+  Add  new  claim.”</p>
</li>
</ul>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/OIDC_Add_Claims_SSO/oidc03.png" alt="OIDC"></p>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/OIDC_Add_Claims_SSO/oidc04.png" alt="OIDC"></p>
<ul>
<li>Enter  the  desired  “name”  for  the  claim  and  select  “user.mailnickname”  as  the  “Source  attribute”  (adjust  according  to  your  specific  needs).  Finally,  click  on  “Save.”</li>
</ul>
<p><strong>Verification</strong></p>
<p>After  completing  the  configuration,  when  logging  in  to  CAS  via  Azure  OIDC,  an  additional  Claim  named  “mailnickname”  will  be  available.</p>
<p><strong>Note:</strong>  If  you  encounter  the  error  message  “This  application  is  required  to  be  configured  with  an  application-specific  signing  key.  It  is  either  not  configured  with  one,  or  the  key  has  expired  or  is  not  yet  valid”  in  CAS,  it  indicates  that  the  Manifest  has  not  been  configured  properly.</p>
<h2 id="reference--resources">Reference  Resources</h2>
<ul>
<li><a href="https://learn.microsoft.com/en-us/answers/questions/390941/app-registrations-vs-enterprise-applications-in-az">App Registrations vs Enterprise Applications in Azure AD - Certificate Significance</a></li>
<li><a href="https://stackoverflow.com/questions/54071385/difference-between-enterprise-application-and-app-registration-in-azure">Difference between “enterprise application” and “app registration” in Azure</a></li>
<li><a href="https://learn.microsoft.com/en-us/entra/identity-platform/reference-app-manifest?WT.mc_id=Portal-Microsoft_AAD_RegisteredApps">Microsoft  Entra  application  manifest</a></li>
<li><a href="https://stackoverflow.com/questions/73746617/when-using-a-custom-azure-claimsmappingpolicy-cant-sign-in-this-application-is">When using a custom Azure ClaimsMappingPolicy can’t sign in “This application is required to be configured with an application-specific signing key. “</a></li>
</ul>

