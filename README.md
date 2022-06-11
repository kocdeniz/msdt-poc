# CVE-2022-30190 MS-MSDT Using Follina Attack Vector 

> Deniz Koc | June 9, 2022

--------------




On May 30, 2022, Microsoft issued CVE-2022-30190 regarding the Microsoft Support Diagnostic Tool (MSDT) in Windows vulnerability. It is basically a remote code execution technique used through MSDT and MS Office program, namely Microsoft Word. This attack takes place using malicious Word documents that execute PowerShell commands via the Microsoft Diagnostic Tool (MSDT). The ‘Follina’ zero-day features a remote code execution that works without elevated privileges, does not require macro enablement to execute binaries or scripts, and can bypass Windows Defender detection.



# How It Entered The Radar?

![Screenshot from 2022-06-09 03-01-49](https://user-images.githubusercontent.com/74410580/172809166-2b45b02b-9dc1-44a2-9fd3-e8e6f6d89ca7.png)


On may 27, there was a tweet by Nao-sec who is security researcher trying different attack vectors targeting cve 2021-40444 which was a previous vulnerability taking advantage mhtml(MIME encapsulation of aggregate HTML document) protocol to get initial access with just a simple office document.

Nao-sec found some compelling document which was using external reference inside the MS word document that would call out to an external HTML file. That HTML file would stage and load powershell code using MSDT Uniform Resource Identifier (URI)  protocol in windows. 



A former Microsoft employed security researcher Kevin Beaumont named the vulnerability “Folina,” because the 0 day code references 0438, that's the place code for Follina, Italy. Beaumont referred to that Defender for Endpoint did now no longer hit upon the exploit, which retrieves an HTML file from a remote webserver and lets in PowerShell code execution.


# Explanation Of The Exploit

This attack vector only needs one thing which a user downloads a malicious office documents and they open it or just navigate to it.
I myself use my ubuntu machine and and download python file that is from John Hammond's  [github page](https://github.com/JohnHammond/msdt-follina)

To create malicious word document and stage a payload with an http server 


```
$ python3 follina.py -i (network interface) -r (port number for reverse shell)  
[+] copied staging doc /tmp/g5y17w2c
[+] created maldoc ./follina.doc
[+] serving html payload on :8000
[+] starting 'nc -lnvp 9001
Listening on 0.0.0.0 9001

```
<br> 


![Screenshot from 2022-06-09 01-46-32](https://user-images.githubusercontent.com/74410580/172817080-43919074-fcaf-4af0-b690-ee3cba32d059.png)



<br>

On windows machine as a victim, open up microsoft word documents as you might any other document except this one in particular includes external reference to an another location and executes it.



![Untitled](https://user-images.githubusercontent.com/74410580/172826268-6170639b-cea7-4639-bef7-dec20a7e7f84.png)

<br>
As expected after running the document on victim machine, it offered initial access under the the priveleges and permissions of the user account that invoked microsoft word.


# How far Could This Exploit Go Further In The Wild?

Well, it might seem very low privelege when I showcased on my virtual machine but it opens the door for threat actor to do lateral movement or upload persistence to maintain access, even escalate priveleges so that they become the administrator,root account etc.

To explain, an html file inside the word document was retrieving by windows machine is the key element of MS RCE(remote code execution) because it includes invoking payload which eventually staging powershell code to be executed encoded in base 64. To see the powershell client script a little bit clearly here is below the encoded and decoded status of the powershell commands inside this html file.


![Screenshot from 2022-06-09 14-00-15](https://user-images.githubusercontent.com/74410580/172833699-01276317-f19e-4049-95e1-5994824faddd.png)

<br>
Considering these powershell commands,a typical adversory behaviour would be fishing campaing, stealing information from local browsers, mail clients and file services, conducts machine recon and then zips it for exfil.Moreover this remote code execution actually can derived into whatever an adversory want.That could be remote access trojan or cryptocurrency miner or ransomware, name it.

# Mitigation Suggestions

Microsoft has recently released [guidance](https://msrc-blog.microsoft.com/2022/05/30/guidance-for-cve-2022-30190-microsoft-support-diagnostic-tool-vulnerability/) on disabling the Microsoft Diagnostic Tool (MSDT) URL protocol. The Microsoft Support Diagnostic Tool (MSDT) is a tool designed to collect information to send to the Microsoft Support but it’s currently unclear how Microsoft intends to respond to the discovery and how quickly a patch will be made available.

![Screenshot from 2022-06-09 15-10-46](https://user-images.githubusercontent.com/74410580/172843719-b5564cf1-6b3b-4ae8-8a0a-247d2c561fcf.png)

<br>

Another option would be to remove the file type association for ms-msdt (can be done in Windows Registry HKCR:\ms-msdt. When the malicious document is opened, Office will not be able to invoke ms-msdt thus preventing the malware from running. Be sure to make a backup of the registry settings before using this mitigation. for more information check out [ twitter post](https://twitter.com/DidierStevens/status/1531033449561264128) 


# Conclusion

There is currently no available legit patch that would come from Microsoft. Until that time,what we learn is that, the indusrty will have a hard time to inspect incoming impact of threats. This vulnerability is brand new, simple and a current zero day that has no concrete fix.


# References

[1] *nao sec's twitter post.* [Online]. Available: https://twitter.com/nao_sec/status/1530196847679401984

[2] *John Hammond Github ‘Follina’ MSDT Attack.*” [Online] Available:https://github.com/JohnHammond/msdt-follina

[3]*NetWitness Community* [Online] Available:   https://community.netwitness.com/t5/netwitness-community-blog/threat-analysis-detecting-follina-cve-2022-30190-rce/ba-p/683866


[3] *Picus Security blog post Huseyin Can Yucel*
[Online] Available: https://www.picussecurity.com/resource/follina-ms-office-zero-day-vulnerability-exploitation-and-simulation


[4]"*To disable the msdt url protocol*" Available:https://msrc-blog.microsoft.com/2022/05/30/guidance-for-cve-2022-30190-microsoft-support-diagnostic-tool-vulnerability/

