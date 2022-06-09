# CVE-2022-30190 MS-MSDT with Follina Attack Vector 

> Deniz Koc | June 9, 2022

--------------


On May 30, 2022, Microsoft issued CVE-2022-30190 regarding the Microsoft Support Diagnostic Tool (MSDT) in Windows vulnerability.It is basically a remote code execution technique used through MSDT and MS Office program, namely Microsoft Word. It was first discovered on may 27 by nao-sec on twitter and he was a security researcher who is looking around virus total for different attack vectors targeting CVE 2021-40444 which was previous vulnerability taking advantage of the mhtml protocol targeting MS documents that could be used initial access for remote code execution





Nao-sec found some compelling document which was using external reference inside the MS word document that would call out to an external HTML file. That HTML file would stage and load powershell code using MSDT protocol in windows.



