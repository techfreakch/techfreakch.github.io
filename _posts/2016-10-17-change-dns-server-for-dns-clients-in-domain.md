---
layout: post
title: "Change DNS Server for DNS Clients in Domain"
date: 2016-10-17
comments: true
author: michel_zehnder
categories: [system-administration]
tags: [dns, windows]
---
Recently we had the need to change DNS Servers on a couple of machines.
Rather than logging into every machine and doing this manually, of course we did it using PowerShell! It even works with IPv6.
```powershell
# Get Computers from Active Directory
$computers = Get-ADComputer -SearchBase "OU=Servers,OU=Computers,OU=Accounts,DC=domain,DC=local" -SearchScope 1 -Filter *

# Connect to each computer an change DNS Settings
foreach ($computer in $computers)
{
    Invoke-Command -ComputerName $computer.DNSHostName -ScriptBlock {
        # Only change settings for adapter which have an 192.168.0.* IP Address
        $addresses = Get-NetIPAddress | Where-Object { $_.IPAddress -like '192.168.0.*' }

        foreach ($address in $addresses)
        {
            $ifindex = $addresses.ifIndex
            Set-DnsClientServerAddress -InterfaceIndex $ifindex -ServerAddresses("192.168.0.231","192.168.0.232","192.168.0.233","2001:db8:dead:beef::231","2001:db8:dead:beef::232","2001:db8:dead:beef::233")
        }
    } -AsJob
}
```
You can watch the progress using
<pre class="theme:powershell lang:ps decode:true ">Get-Job</pre>
Simple, and pretty fast.

&nbsp;