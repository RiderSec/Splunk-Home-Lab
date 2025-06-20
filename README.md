# Splunk-Home-Lab
As a way to expand my knowledge on Splunk, SPL and how logs are ingested in Splunk, I made this project.

I learned how to:
- Ingest logs from a machine, from windows event viewer and sysmon.
- Set up Splunk on a machine.
- Basic SPL queries.

## Main Idea

Three machines:
  - Ubuntu (Splunk Host)
  - Windows 10 (Victim machine)
  - Kali (Attacker)

Kali machine serves as an attacker to test out any attack we would seek to find on our Splunk logs, the Windows 10 machine is set up with **Universal Forwarder**, so we can get the logs coming from it to our Splunk host machine.
