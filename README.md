# CloneWindowsIdentity
Install a Windows 11 with a specific machine SID

\
Manual Steps:
------------

1. Start Windows installation
2. When the installation enters OOBE, enter Audit Mode (ctrl-shift-f3)
3. In Audit mode, close the running Sysprep window.
4. Run Sysprep generalize but do not restart yet:

       start /wait %windir%\System32\sysprep\sysprep.exe /generalize /oobe /quit

5. Add `SidAccountDomainNew` with the SID in hex form:
   
       reg add HKLM\SYSTEM\Setup\SetupCl\PendingRequest /v SidAccountDomainNew /t REG_BINARY /d 0104000000000005150000005E4E90FE4AC1064C79BFA70C /f

6. Restart the computer

\
The setup will resume OOBE with the specified custom machine SID

\
How to convert SID in hex form?
-------------------------------

To convert a SID to binary hex format, e.g. `S-1-5-21-4270870110-1275511114-212320121`:

    powershell -Command "$sid=[System.Security.Principal.SecurityIdentifier]::new('S-1-5-21-4270870110-1275511114-212320121');$bytes=[array]::CreateInstance([byte],$sid.BinaryLength);$sid.GetBinaryForm($bytes,0);-join ($bytes|ForEach-Object { $_.ToString('X2') })"

This will output: `0104000000000005150000005E4E90FE4AC1064C79BFA70C`

\
Do I really have to do it manually?
-----------------------------------
Of course not! See my [autounattend.xml](unattend/autounattend_sidmachine.xml)


\
Why my *USER* SID has RID 1002 ?
-------------------------
Hey! I followed the manual steps and the user created by OOBE has RID 1002 (and not 1001 like during a normal setup).
\
I got you covered -> use an [autounattend](https://github.com/davidedg/CloneWindowsIdentity#do-i-really-have-to-do-it-manually) file (either the minimal one I shared, or you can also add users there)


\
Credits:
--------
[https://serverfault.com/questions/1070137/assign-a-specific-machine-sid-to-a-new-windows-system/1070138#1070138](https://serverfault.com/questions/1070137/assign-a-specific-machine-sid-to-a-new-windows-system/1070138#1070138)
\
[https://superuser.com/questions/1721502/assign-a-specific-machine-sid-to-a-new-windows-installation-using-sysprep](https://superuser.com/questions/1721502/assign-a-specific-machine-sid-to-a-new-windows-installation-using-sysprep)
\
[https://schneegans.de/windows/unattend-generator](https://schneegans.de/windows/unattend-generator)
