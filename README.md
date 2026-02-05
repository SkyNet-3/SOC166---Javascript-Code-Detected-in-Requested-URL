<h1>SOC166 - Javascript Code Detected in Requested URL</h1>


<h2>Description</h2>
This lab reviews a possible XSS payload alert triggered by SOC166 – JavaScript Code Detected in Requested URL. The HTTP request seems to contain a modified version of a common XSS payload <$script>javascript:$alert(1)<$/script> Javascript code in the URL parameter. This type of JavaScript injection is commonly used by attackers as a probing technique to determine whether an application is vulnerable to XSS. This behavior is most likely what triggered the security rule. Unfortunately, the request was allowed by the server. Lets analysis the alert and takes a closer look at the potential impact.
<br />


<h2> Utilities Used</h2>

- <b>LetsDefend SIEM</b> 
- <b>LetsDefend Email Security</b>
- <b>LetsDefend EDR</b>
- <b>URL Decoder</b>
- <b>VirusTotal</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (22H2)

<h2>Alert walk-through:</h2>

<p align="center">
Go to investigation channel in Monitoring to get the context of the alert: <br/>
<img src="https://i.imgur.com/xjLKE6k.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
After taking ownership of the case, very simply look at the rule name and examine the direction of the traffic thats occuring between both devices:  <br/>
<img src="https://i.imgur.com/JWl6L2J.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
The specific pattern/signature that the rule is detecting is <$script>javascript:$alert(1)<$/script>, which is the JavaScript code that triggered the alert. There are also several inbound HTTPS requests being made from an external IP address. After taking a closer look at each request, it appears to be a typical recon/probing attempt involving various XSS payloads, encodings, response size changes, and 302 status codes : <br/>
<img src="https://i.imgur.com/HKukriQ.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/QN2SgqT.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/e3qUBKQ.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/WfisZ0W.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
CHINA UNICOM is the owner. This is inbound traffic. It is a Pool addresses and its a web hosting cloud provider. IP address is listed has a -14 community score. Traffic is not coming from company network, its external. Hostname: WebServer1002. The owner is webadmin15 (admin account) last login was on Feb, 02, 2022, 03:40 PM: <br/>
<img src="https://i.imgur.com/AcLpWk1.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/Qh9AXgg.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Now we'll examine the encoded XSS payloads embedded in the URL parameter:  <br/>
<img src="https://i.imgur.com/RjCFTPV.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
As stated earlier, when analyzing all the requests in the logs we see alot of diffrent XSS recon/probing payloads being deployed to the server.  <br/>
<img src="https://i.imgur.com/RkKo7Nq.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/OQ3BLhc.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/qU8tHwN.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/QLRNSKq.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
The traffic was malicious because it contained the XSS payloads:  <br/>
<img src="https://i.imgur.com/sCujGob.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was XSS:  <br/>
<img src="https://i.imgur.com/u1dOxvU.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Email Security was checked to determine whether this alert was triggered by a scheduled pen test or a security simulation. After searching the inbox for the hostname, IP address, and username, no emails were found validating this activity as planned work:  <br/>
<img src="https://i.imgur.com/TowIxaS.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/15aDW3y.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an inbound HTTP request. Internet to Company Network:  <br/>
<img src="https://i.imgur.com/h3wEizW.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
check if the attack was successful:  <br/>
<img src="https://i.imgur.com/toHZfZt.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Although the XSS payloads requests were allowed by the device, after reviewing the web server’s endpoint security data (i.e. network actions and browser history) no further actions like outbound connections to suspicous domains, sudden redirects, or requests containing cookie-looking data were observed on the server : <br/>
<img src="https://i.imgur.com/aTgzh4B.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/GEf1Ixz.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Attack was unsuccessful. The payload was delivered, but input sanitization or output encoding prevented script execution:  <br/>
<img src="https://i.imgur.com/ClHmwIZ.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Add the artifacts that were examined in the investigation :  <br/>
<img src="https://i.imgur.com/ETRm6rx.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an unsuccesful attack, so no further escalation is needed :  <br/>
<img src="https://i.imgur.com/wjPCpa8.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
write your analyst notes from the case. click confirm and youre finished! Next closing the case..:  <br/>
<img src="https://i.imgur.com/DAL8HJX.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Lastly, the alert triggered was a True Positive because the security system correctly identified this as unplanned malicious XSS payloads being delivered to the web server :  <br/>
<img src="https://i.imgur.com/WmQCfs4.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
