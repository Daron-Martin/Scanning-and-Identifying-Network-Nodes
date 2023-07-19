<h1>Scanning and Identifying Network Nodes</h1>

<h2>Description</h2>
In this activity, I will use common network tools, such as local commands and the Nmap network mapper, to discover other hosts on the local network. Next, I will conduct a banner grabbing exercise to identify specific services on the hosts. Finally, I will use DNS tools to gather name resolution information.
<br />


<h2>Utilities Used</h2>

- <b>Command prompt</b> 
- <b>Nmap</b>

<h2>Environments Used </h2>

- <b>Windows 10 server</b> 
- <b>Kali Linux</b>

<h2>Program walk-through:</h2>

<p align="center">
On the PT1-Kali VM, sign in as root.
From the top bar, select the Terminal icon button.
Run the ifconfig command to display the interface configuration. Note the IP address assigned to the eth0 interface.
ifconfig: <br/>
<img src="https://i.imgur.com/JXblAyr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/rjjk9LE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the ip a command to display the same information using the newer ip a tool:
ip a
The ifconfig and ip a commands.
10.1.0.192 is the IP address for the eth0 adapter of the PT1-Kali Linux virtual machine
Run the following command to identify the default gateway:
ip route show
Because the network uses DHCP to provide client addresses, the local machine has been configured with a default gateway address automatically.
10.1.0.254 is the IP address of the router default gateway.:  <br/>
<img src="https://i.imgur.com/QgdDNUG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the arp -a command to check the ARP cache to display other hosts local to this subnet.
arp -a
The IP address for the DC1.corp.515support.com virtual machine is 10.1.0.1
Run the ip neighbor command to display similar information using the newer ip tool.
ip neighbor
The ARP cache shows only machines that have communicated with the local host. To verify whether any other hosts are present, you can perform a "sweep" of the local network. One means of doing this is to use ping in a for/next loop. You can also use the netdiscover tool bundled with Kali.: <br/>
<img src="https://i.imgur.com/zEmPF0T.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to scan the network by using netdiscover. The results should discover several other hosts connected to the vLOCAL switch.
netdiscover -i eth0 -r 10.1.0.0/24
Press q to exit the Netdiscover report and return to the command prompt.
Run netdiscover -h to view the help page. The tool can operate in a passive mode, but you do not need to be stealthy, so you will run an active scan.:  <br/>
<img src="https://i.imgur.com/O14mPDR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Switch to the DC1 VM, send Ctrl+Alt+Delete, and then login as 515support\Administrator.
Right-click the Start menu and select Command Prompt (Admin). 
Run the following command to display the IP address configuration for DC1:
ipconfig.
The IP address for the DC1 virtual machine is 10.1.0.1:  <br/>
<img src="https://i.imgur.com/0Jm53PA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to test the reliability (packet loss) and latency (delay) of the connection between the DC1 VM and the PT1-Kali VM (the test takes 30-45 seconds to run):
pathping 10.1.0.192
0 percentage of packets were lost during this test:  <br/>
<img src="https://i.imgur.com/rse0pMr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Use nmap to discover hosts
From a penetration tester or threat actor perspective, network reconnaissance will typically aim to discover the following:
- Default gateway (the router connecting the subnet to other networks).
- DNS server (used to resolve host names on the network).
- Whether any network directory/authentication and application servers are present.
- Whether any host/client access devices are present.
- Whether any other types of devices (embedded systems or appliances) are present.
Use Nmap from the PT1-Kali VM to report this information for this network.
Switch back to PT1-Kali. 
Run the following command to scan the Kali VM: nmap localhost:  <br/>
<img src="https://i.imgur.com/ldY4LmN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to do a basic network scan:
nmap 10.1.0.0/24:  <br/>
<img src="https://i.imgur.com/FUQA1wK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command and check the output. What services are running and what do they tell you about the host?
nmap -sS 10.1.0.254
This syntax will scan the default port range (1000 ports) on the target.
SSH and DNS services are running on this virtual machine:  <br/>
<img src="https://i.imgur.com/U6E0Fke.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to identify more about the host: nmap -A 10.1.0.254
Linux operating system is running on the 10.1.0.254 host:  <br/>
<img src="https://i.imgur.com/w60os6n.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to scan for open ports 20-200 on the network: nmap -p 20-200 10.1.0.0/24
Port 80 is open on MS1:  <br/>
<img src="https://i.imgur.com/Q1OOZL7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to scan for the twenty most common ports: nmap --top-ports 20 10.1.0.0/24:  <br/>
<img src="https://i.imgur.com/1mf12m3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>  
<br />
<br />
Run the following command to quickly scan the network for hosts that are up or down on the network:
nmap -sn 10.1.0.0/24
MS1 is configured as an email server:  <br/>
<img src="https://i.imgur.com/OQLROTw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command in the Terminal to connect to the 10.1.0.1 HTTP server by using cURL:
curl -s -I 10.1.0.1
IIS 10 web server service and version is used on the target:  <br/>
<img src="https://i.imgur.com/d6hfFUN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the dig command in the Terminal to perform a reverse lookup on the default gateway.
dig -x 10.1.0.254
10.1.0.1 is the IP address of the DNS server that answers the query:  <br/>
<img src="https://i.imgur.com/YZNDTHD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Ms1.corp.515support.com is the fully-qualified domain name (FQDN) of the VM found at 10.1.0.2 :  <br/>
<img src="https://i.imgur.com/weDe85u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Run the following command to display the authoritative DNS server for the namespace: dig soa corp.515support.com
The SOA records.
The query returns the FQDN of the DNS server responsible for the domain (DC1.corp.515support.com) and its host record (10.1.0.1). It's also worth noting some of the flags shown:
<br />
<br />
aa indicates that the answer is authoritative. The "AUTHORITY" section of the response is empty. Contents for this section are commonly omitted by name servers to reduce the size of responses.
<br />
ra indicates that recursion is available; that is, this router will forward queries to other servers.
<br />
Close the terminal window.:  <br/>
<img src="https://i.imgur.com/zGAhTr3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
