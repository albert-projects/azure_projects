---


---

<h2 id="azure--web--api--returning--404--errors--for--put--and--delete--calls">Azure  Web  API  Returning  404  Errors  for  PUT  and  DELETE  Calls</h2>
<h3 id="problem">Problem</h3>
<p>Recently,  after  deploying  an  <a href="http://ASP.NET">ASP.NET</a>  Core  application  to  Azure  Web  App,  we  encountered  an  issue  during  testing.  While  GET  and  POST  requests  functioned  correctly,  invoking  DELETE  resulted  in  a  404  error.</p>
<h3 id="solution">Solution</h3>
<p>The  chosen  deployment  platform  for  this  application  was  Windows  IIS,  and  it  appears  that  the  issue  stemmed  from  IIS  WebDAV  functionality.  To  rectify  this,  we  needed  to  remove  the  WebDAV  module  from  the  web.config  file.</p>
<p>Fortunately,  Azure  provides  a  convenient  way  to  modify  the  web.config  directly  through  the  App  Service  Editor  within  the  Azure  Portal.</p>
<p><strong>Steps:</strong></p>
<ol>
<li>
<p>Access  the  Azure  Portal  and  navigate  to  your  App  Service.</p>
</li>
<li>
<p>Under  “Development  Tools,”  select  “App  Service  Editor  (Preview)”.</p>
</li>
<li>
<p>Open  the  web.config  file  for  editing.</p>
</li>
<li>
<p>Locate  the    and    sections  within  &lt;system.webServer&gt;.</p>
</li>
<li>
<p>Add  the  following  lines  to  remove  the  WebDAV  module  and  handler:</p>
</li>
</ol>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Web-API-Returning-404-Error/app-api-err01.png" alt="404"></p>
<p><img src="https://github.com/albert-projects/azure_projects/blob/master/Web-API-Returning-404-Error/app-api-err02.png" alt="404"></p>
<pre><code>&lt;modules&gt;
    &lt;!-- Remove WebDAV Module --&gt;
    &lt;remove name="WebDAVModule" /&gt;
&lt;/modules&gt;
&lt;handlers&gt;
    &lt;!-- Remove WebDAV handler --&gt;
    &lt;remove name="WebDAV" /&gt;
    &lt;!-- other handlers ... --&gt;
&lt;/handlers&gt;
</code></pre>
<ol>
<li>Save  the  changes  to  the  web.config  file.</li>
</ol>
<p>After  implementing  these  modifications,  PUT  and  DELETE  calls  should  function  as  expected  without  returning  404  errors.</p>
<p><strong>Note:</strong>  We  extend  our  gratitude  to  Garry  Jiang  for  their  assistance  in  resolving  this  issue.  <sup>_</sup></p>
<p><strong>Reference  Resource:</strong></p>
<ul>
<li><a href="https://stackoverflow.com/questions/48188895/asp-net-core-with-iis-http-verb-not-allowed">ASP.NET  Core  with  IIS  -  HTTP  Verb  Not  Allowed</a></li>
</ul>

