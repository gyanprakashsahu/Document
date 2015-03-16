#### If, You Installed Linux On Windows And Then Remove Linux And Your System Fire An Alert "OS NOT FOUND" OR Similer To This, Follow Below Steps:

```
Boot from the Windows 7 CD/DVD/Bootable Pendrive you have and choose the repair option when asked. No problems will be found, but when asked select 'command prompt' and then enter the following:

bootrec /fixmbr

bootrec /fixboot

```
