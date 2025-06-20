# Summary
As a way to expand my knowledge on Splunk, SPL and how logs are ingested in Splunk, I made this project.

I used VMWare for this project, making 3 VMs locally to test with.

I learned how to:
- Ingest logs from a machine, from windows event viewer and sysmon.
- Set up Splunk on a machine.
- Make basic SPL queries to find malicious activity such as shells, files created etc.

## Main Idea

Three machines:
  - Ubuntu (Splunk Host)
  - Windows 10 (Victim machine)
  - Kali (Attacker)

Kali machine serves as an attacker to test out any attack we would seek to find on our Splunk logs, the Windows 10 machine is set up with **Universal Forwarder**, so we can get the logs coming from it to our Splunk host machine.

Ubuntu machine serves to run Splunk.

Network diagram:
![image](https://github.com/user-attachments/assets/75ea119f-a992-4379-bdd9-db0d98b37e88)

## Setup

Fairly easy, simply creating the kali VM for a start, putting it in a network using VMWare's Virtual Network Editor and disabling DHCP on it so the IPs are static.

For the Ubuntu Splunk host we'll go on Splunk's website to download **Splunk Enterprise**, install and start it.

The windows machine requires some more attention, needing to download the universal forwarder. During setup we will need to input the IP of our Ubuntu host, which in this case is `192.168.56.30`. We can leave the port blank here.

We'll now need to setup sysmon, downloading it from the official microsoft website, then extract the files from the `.zip` file we downloaded.

Sysmon needs to be setup with a config, we will use SwiftOnSecurity's `sysmonconfig.xml` to set up sysmon.

Once this is set up and verified to be running, we should create an `outputs.conf` and `inputs.conf` file in the `C:\Program Files\SplunkUniversalForwarder\etc\system\local` directory.

The `inputs.conf` file will look something like this, and will be to tell Splunk where we want to look on this machine for logs:

```
[WinEventLog://Security]
disabled = 0

[WinEventLog://System]
disabled = 0

[WinEventLog://Application]
disabled = 0

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = sysmon
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

```

In short, this tells the UF (Universal Forwarder) to look for logs in the event log, and to look for logs related to Security, System and Application. The last entry will be the one to help us ingest logs from Windows Sysmon, which we will also create an index for.

The `outputs.conf` file will look like so and will tell the UF where to send these logs:

```
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 192.168.56.30:9997

```

This makes a group, and tells UF the IP and port to which to send the logs to `9997` is the default port for Splunk log ingestion.

We will also need to create an outbound firewall rule to make sure our data actually makes it to our Ubuntu Splunk machine.

For this, simply go to the windows firewall settings, click on advanced and create an outbound rule that allows us to send traffic to `192.168.56.30` over port `9997`.

Once this is all done, we just have to restart the SplunkForwarder service, and then we can check if the logs are ingesting correctly by querying `index=main` on our Splunk app.

If all has gone well, you now have a working HomeLab!


