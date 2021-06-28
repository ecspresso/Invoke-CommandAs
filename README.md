# Forked and made into a single file.

# Invoke-CommandAs

```
.SYNOPSIS

    Invoke Command as System/User on Local/Remote computer using ScheduleTask.

.DESCRIPTION

    Invoke Command as System/User on Local/Remote computer using ScheduleTask.
    ScheduledJob will be executed with current user credentials if no -AsUser <credential> or -AsSystem is provided.

    Using ScheduledJob as they are ran in the background and the output can be retreived by any other process.
    Using ScheduledTask to Run the ScheduledJob, since you can allow Tasks to run as System or provide any credentials.
    
    Because the ScheduledJob is executed by the Task Scheduler, it is invoked locally as a seperate process and not from within the current Powershell Session.
    Resolving the Double Hop limitations by Powershell Remote Sessions. 

```
## Examples

```powershell
# Execute Locally.
Invoke-CommandAs -ScriptBlock { Get-Process }

# Execute As System.
Invoke-CommandAs -ScriptBlock { Get-Process } -AsSystem

# Execute As a GMSA.
Invoke-CommandAs -ScriptBlock { Get-Process } -AsGMSA 'domain\gmsa$'

# Execute As Credential of another user.
Invoke-CommandAs -ScriptBlock { Get-Process } -AsUser $Credential

# Execute As Interactive session of another user.
Invoke-CommandAs -ScriptBlock { Get-Process } -AsInteractive 'username'

```
### You can execute all the same commands as above against a remote machine.
### Use -ComputerName/Credential or -Session to authenticate
```powershell
# Execute Remotely using ComputerName/Credential.
Invoke-CommandAs -ComputerName 'VM01' -Credential $Credential -ScriptBlock { Get-Process }

# Execute Remotely using Session.
Invoke-CommandAs -Session $PSSession -ScriptBlock { Get-Process }

# Execute Remotely using PSSession and execute ScriptBlock as SYSTEM.
Invoke-CommandAs -Session $PSSession -ScriptBlock { Get-Process } -AsSystem

# Execute Remotely on multiple Computers at the same time.
Invoke-CommandAs -ComputerName 'VM01', 'VM02' -Credential $Credential -ScriptBlock { Get-Process }

# Execute Remotely as Job.
Invoke-CommandAs -Session $PSSession -ScriptBlock { Get-Process } -AsJob
```

## How to see if it works:
```powershell
$ScriptBlock = { [System.Security.Principal.Windowsidentity]::GetCurrent() }
Invoke-CommandAs -ScriptBlock $ScriptBlock -AsSystem
```

```
One liner (dont write to disk):
```
"Public/Invoke-CommandAs.ps1", "Private/Invoke-ScheduledTask.ps1" | % {
    . ([ScriptBlock]::Create((New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/mkellerman/Invoke-CommandAs/master/Invoke-CommandAs/${_}")))
}
```
