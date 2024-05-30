# SIEM-Implementation-in-Azure-Cloud

# Description
This project demonstrates the integration of various Microsoft Azure services to create a robust security monitoring solution. By provisioning a Windows 10 VM, configuring Azure Log Analytics, Microsoft Defender for Cloud, and Azure Sentinel, and employing PowerShell to automate log collection and API interactions, the project provides practical experience in cloud security and SIEM (Security Information and Event Management) systems. The final output is a dynamic map in Azure Sentinel that visualizes the geographical locations of failed logon attempts, offering valuable insights into potential security threats.

## Utilities Used
- **Microsoft Sentinel (SIEM):** A scalable, cloud-native SIEM and SOAR solution that provides intelligent security analytics and threat intelligence across the enterprise.
- **Log Analytics Workbooks:** Interactive and customizable tools for querying, visualizing, and analyzing data collected by Azure Monitor.
- **Microsoft Defender for Cloud:** A comprehensive security management solution that protects Azure and hybrid cloud workloads.
- **Virtual Machines:** Scalable computing resources available on-demand for running applications and services.
- **Remote Desktop:** A protocol used to connect and manage the virtual machine.
- **PowerShell:** A scripting language and automation framework used to automate tasks and manage systems.
- **APIs:** Application Programming Interfaces used to interact with external services, such as IPgeolocation.io for geolocation data.
- **Event Viewer:** A component of Windows operating systems that lets administrators and users view event logs on a local or remote machine.
- **Firewalls:** Security systems designed to monitor and control incoming and outgoing network traffic based on predetermined security rules.

## Environments Used:
- **Microsoft Azure**
- **Windows 10 (21H2)**

## Links
- [Microsoft Azure Free Trial](https://azure.microsoft.com/en-us/free/)
- [IPGeolocation](https://ipgeolocation.io/)

# Program Walk-through
The first step in this project is to create a Microsoft Azure account, which will serve as the cloud environment for provisioning the necessary resources. By creating an Azure account, I will receive a $200 credit, which will be utilized for this project. Given that the resources required are not resource-intensive, the credit can also be applied towards future projects. Additionally, I will use IPgeolocation.io for obtaining IP geolocation data, which will be crucial for visualizing the origins of failed logon attempts.


![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s1.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s2.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s3.jpg?raw=true)

# Next Steps
The next step involves starting the process of creating virtual machines (VMs) in Microsoft Azure. Since provisioning a VM can be time-consuming, initiating this step early allows the VM to be set up in the background while other tasks are being completed.


![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s4.jpg?raw=true)

## Resource Group Creation
At this point in the VM creation process, it is essential to create a new Resource Group for all future resources. A Resource Group in Azure is a logical grouping of tools, services, configurations, and more that exist under one banner, allowing them to be created and deleted simultaneously. This grouping simplifies resource management by ensuring that all related resources share the same lifespan. Resources outside a particular Resource Group will remain intact even if the Resource Group is deleted. For this project, I named the Resource Group "HoneyPot_Lab" and the virtual machine "HoneyPot-VM".

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s5.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s6.jpg?raw=true)

## VM Configuration
Continuing with the VM creation process, I need to select the appropriate size for the VM. Initially, I chose Standard_B1s (as indicated in the photo), but later upgraded to Standard_B2s for better performance, providing 2 CPU cores and more RAM. The initial choice was too slow, causing frequent PowerShell crashes and significant lag.

After selecting the VM size, I created the admin account with a unique username and a robust 30-character password consisting of special characters, numbers, and a mix of lowercase and uppercase letters. Given that the exposed VM would be a target for brute force and dictionary attacks, a strong password was essential.

Next, I configured the public inbound port rules, which define which ports will be open for connections to the VM. Although multiple authentication methods such as SSH can be set at this step, I opted to allow only RDP (Remote Desktop Protocol), which uses port 3389.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s7.jpg?raw=true)

## Network Security Group (NSG) Configuration
The next step involves creating a new Network Security Group (NSG). An NSG acts as a firewall that can enforce rules on inbound and outbound traffic to Azure resources. For this honeypot project, the objective is to allow unrestricted access to the VM to observe all incoming traffic.

### Create a New Inbound Rule
To achieve this, I removed the default inbound rule and created a new one to allow all traffic. By setting the Destination Port Ranges to "*", any port ranges are permitted, and allowing any protocol ensures all types of traffic are accepted. With a priority of 100, this rule takes precedence over others, ensuring unrestricted access to the VM. It's important to highlight that while this setup is suitable for a honeypot project, it's not recommended for real production environments due to significant security risks.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s8.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s9.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s10.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s11.jpg?raw=true)

I review, create, and deploy the VM as the last step.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s12.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s13.jpg?raw=true)

## Setting up Log Analytics Workspace
While my VM is deploying, I can get started on setting up Log Analytics Workspace. These can all be found in the Azure home dashboard, or you can search for them in the search bar. When I create a Log Analytics Workspace, I make sure to put it in my HoneyPot_Lab resource group so it can be deleted when I delete that resource group. I name the instance LAW-HoneyPot.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s14.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem21.png?raw=true)

## Provisioning Microsoft Defender for Cloud
Next, I navigate to Microsoft Defender for Cloud to provision and enroll in the necessary plans for data collection and aggregation for Microsoft Sentinel. This involves connecting my HoneyPot-VM to Microsoft Defender using Data Connectors. I specifically enable the plans for Foundational CSPM (Cloud Security Posture Management) and Servers, as I am not running any SQL servers. With my VM now created, it can be connected to these services to ensure data is collected and available for further analysis.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s15.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s16.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s17.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s18.jpg?raw=true)

Since my VM was created, I can go back into Log Analytics Workspaces and connect my VM to that service as well.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem22.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem23.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem24.png?raw=true)

I can now create a Microsoft Sentinel resource and connect it to my VM.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s19.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/s20.jpg?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem25.png?raw=true)


## Accessing the VM
Now that everything is set up in the Azure dashboard, I can go into my VM and set things up there. The first thing I need to do is get my VM's public IP address so I can Remote Desktop (RDP) into it. I go into the Virtual Machines tab in Azure and navigate into the HoneyPot VM. Highlighted in my VM's Public IP.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem26.png?raw=true)

## Accessing the VM via Remote Desktop
From here, I can log into my VM via Remote Desktop. I open up Remote Desktop on my native PC, enter the public IP address and credentials needed, and connect in! I configure the HoneyPot a bit.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem27.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem28.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem29.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem30.png?raw=true)


## Accessing Event Viewer
After successfully logging into the Virtual Machine (VM) via Remote Desktop Protocol (RDP), I proceeded to access the Event Viewer. The Event Viewer is a critical tool for monitoring and logging all activities on a Windows system. Each action is assigned a specific Event ID, facilitating efficient navigation and review of events.

For this project, our primary focus is on Event ID 4625, which corresponds to failed logon attempts. These logs are crucial for identifying unauthorized access attempts and can be found under the Security tab in Event Viewer.

To demonstrate this, I initiated another RDP session and intentionally used an incorrect password to log into the VM. This action generated a failed logon attempt, which was subsequently recorded in the Event Viewer. By locating and reviewing Event ID 4625 entries, we can analyze the details of each failed logon attempt, including the time of occurrence and the user account involved. This information is essential for maintaining the security and integrity of the VM.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem31.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem32.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem33.png?raw=true)


## Ping Testing and Firewall Configuration
The next step in the process is to ping the VM from my native computer. This step is undertaken to determine if a direct connection can be established with the VM and to check its discoverability. In this instance, the ping attempt was unsuccessful.

The VM did not respond to the ping requests because Windows Defender Firewall is active on the VM. The firewall is configured to block ICMP Echo Requests, which is why the VM is currently undiscoverable. This conclusion was reached by pinging the VM's public IP address, which is 20.162.106.240, and observing multiple "Request timed out" responses. These responses indicate that the firewall is dropping the ICMP request packets, thereby preventing any external discovery or connection attempts via ping.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem34.png?raw=true)

To summarize, the VM's firewall settings are effectively blocking ICMP Echo Requests, ensuring that the VM remains undiscoverable through this method. This behavior is a direct result of the security configurations implemented by Windows Defender Firewall.

To ensure my VM is discoverable on the internet, I disabled the Windows Defender Firewall. I accessed the firewall settings by searching `wf.msc` in the Windows search bar and then systematically disabled all firewall protections. Afterward, I opened the Command Prompt on my native computer and pinged the VM's public IP address, 20.162.106.240. This time, the ping requests were successful, receiving replies from the VM, confirming that disabling the firewall allowed the ICMP requests to pass through, making the VM discoverable.
![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem35.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem36.png?raw=true)

## Setting Up the PowerShell Script
With my VM now exposed to the internet, I can proceed with setting up my PowerShell script, which is the core component of this project. This PowerShell script will parse the Event Viewer specifically for Event ID 4625, which logs failed logon attempts. Upon identifying these events, the script will extract the IP addresses associated with the failed attempts and send them to the website IPgeolocation.io via an API. This approach is chosen because the IP addresses in the Event Viewer lack geographical information. Leveraging IPgeolocation.io allows for efficient retrieval of this data without needing to develop a geolocation service from scratch. The script will then receive the geographical data from the API and save it as a string in a logfile named failed_rdp.log. This logfile will be crucial later in the project for mapping the attacks live in Microsoft Sentinel, providing real-time visualization of the geographical origins of the failed logon attempts.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem38.png?raw=true)

## Setting Up the PowerShell Script and Obtaining API Key
After opening PowerShell, I pasted the pre-written script and saved it to the desktop as `Log_Exporter.ps1`. Next, I created a profile on IPgeolocation.io to obtain my API key, which is necessary for the script to function correctly. This API key should be inserted into the script in the designated area.

Given the limited number of free API calls (1000), it's prudent to conserve them during the setup process. Therefore, I recommend re-enabling the Windows Firewall until the Log Analytics Workbooks Custom Fields are fully configured later in the lab. This precaution will minimize unnecessary API calls from potential external login attempts.

Once the custom fields are set up in Log Analytics Workbooks, providing the necessary infrastructure to monitor and analyze the data effectively, you can then turn off the firewall to allow continuous data collection and monitoring. This approach ensures efficient use of the free API calls while setting up the necessary configurations for monitoring failed logon attempts.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem39.png?raw=true)


## Executing Log_Exporter PowerShell Script
I executed my Log_Exporter PowerShell script, which was designed for better readability by outputting in pink and black text. The script worked as intended, as shown in the first photo, where it successfully processed the first failed login attempt recorded earlier. The output includes the necessary details of the failed login attempt.

### Output in PowerShell
Include description of the output, highlighting the necessary details

### Saving Data to failed_rdp.log
In the second photo, you can see how the script saves the data into the failed_rdp.log file in string format. This log file contains sample data to enhance the training of AI in Log Analytics Workbooks and Microsoft Sentinel. Including a diverse set of data is essential for improving the precision and reliability of the AI's analysis and response capabilities.

By collecting and logging this information, we can later visualize and analyze the geographical origins of failed logon attempts, providing valuable insights into potential security threats and patterns. This setup ensures that we have a robust dataset for training and real-time monitoring, facilitating proactive security measures.


![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem40.png?raw=true)


![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem41.png?raw=true)


## Testing the PowerShell Script
To test the PowerShell script, I initiated another failed logon attempt, only to find that someone from Tunisia had already discovered my VM and was attempting to brute-force it. Although I considered blocking the IP or re-enabling the firewall until my setup was complete, I allowed the attempts to gather more data for AI training in Azure. 

### Costly Decision
This decision proved costly, as the persistent brute-force attempts quickly exhausted the free 1000 API calls provided by IPgeolocation.io, disrupting my project. Consequently, I had to spend $15 for an additional 150,000 API calls to continue and ensure the project's success.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem42.png?raw=true)

## Integrating failed_rdp.log into Log Analytics Workbooks
With confirmation that my PowerShell script is functioning correctly, I proceeded to Log Analytics Workbooks to create a custom log, enabling the integration of my failed_rdp.log into Log Analytics. In Log Analytics, I navigated to my VM and initiated the creation of a Legacy Custom Log. The setup process required a sample log, which was located on the VM.

Since direct download of the logfile from the VM to my native computer was not possible, I followed these steps:

1. Opened the failed_rdp.log file inside the VM.
2. Copied the contents of the logfile.
3. Pasted the copied contents into Notepad on my native computer.
4. Saved the Notepad file to my desktop.

Once the sample log was prepared on my native computer, I imported it into Log Analytics Workbooks. This sample data will be crucial for training Log Analytics, ensuring it can accurately process and analyze future log entries from the failed_rdp.log.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem43.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem44.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem45.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem46.png?raw=true)


## Configuring Log Analytics Custom Log
Next, Log Analytics requires the collection path to access the logfile residing in the VM. The correct path to the file is `C:\ProgramData\failed_rdp.log`. Ensuring accuracy in this path is crucial for Log Analytics to successfully collect log information.

Moving forward, I named the custom log "FAILED_RDP_WITH_GEO," with the .CL (Custom Log) extension automatically appended to it. This name essentially serves as the table name when querying the database later.

With all necessary details provided, I created the custom log, enabling Log Analytics to start collecting and analyzing data from the `failed_rdp.log` file for enhanced monitoring and detection of failed RDP attempts with geographical information.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem47.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem48.png?raw=true)

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem49.png?raw=true)

## Successful Synchronization and Field Extraction
The synchronization between the VM and Log Analytics is successful, as evidenced by the information displayed when querying the newly created FAILED_RDP_WITH_GEO custom log. This confirmation indicates that data is being efficiently sent and received, ensuring that Log Analytics is actively collecting and processing information from the failed_rdp.log file. This functionality is crucial for ongoing monitoring and analysis of failed RDP attempts, facilitating proactive detection and response to potential security threats.

To ensure effective utilization of the data in Microsoft Sentinel, I initiated the process of extracting fields from the failed_rdp.log log. This involved right-clicking on a failed RDP login log containing all the raw data generated by my PowerShell script and highlighting the specific data points I wanted to extract. Each highlighted data point was then assigned a corresponding field name, indicating where that data would be stored.

Following the extraction, the Log Analytics AI automatically analyzed the sample data and actual logs to identify and extract the correct data points. However, it was necessary to manually review and correct any errors made by the AI. This involved using custom KQL queries to separate the raw data column into specific fields such as latitude, longitude, destination host, username, source host, state, country, label, and timestamp.

By meticulously refining and organizing the extracted fields, I ensured the accuracy and relevance of the data for further analysis and detection in Microsoft Sentinel.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem50.png?raw=true)

## Integration of Customized Code into Microsoft Sentinel Workbook
To integrate the customized code into a new workbook in Microsoft Sentinel, I accessed the Workbooks section within the Sentinel portal and initiated the creation process. After selecting the appropriate data source, typically the Log Analytics workspace containing the FAILED_RDP_WITH_GEO custom log, I wrote custom KQL queries to extract the desired fields from the raw log data. These queries were crafted to separate the raw data column into key fields such as latitude, longitude, destination host, username, source host, state, country, label, and timestamp.

Leveraging various visualization options available in the workbook, including tables, charts, and maps, I presented the data effectively to facilitate comprehensive analysis and monitoring of failed RDP attempts with geographical information. Finally, I saved and published the workbook to make it accessible to other users in the organization, enabling collaborative threat detection and response efforts within the Sentinel environment.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/siem51.png?raw=true)


## Visualization of Failed RDP Attacks on a Map
After querying the desired data points, I proceeded to choose the visualization format, opting for a map representation to visualize the failed RDP attacks. Within the workbook settings, I selected the map setting and configured it to plot the attacks based on latitude and longitude coordinates.

Given that some attacks lacked country information, I decided against plotting them solely by country. To enhance visual clarity, I adjusted the metric settings to scale the size of the bubbles based on event counts, ensuring that larger bubbles represented areas with a higher frequency of attacks.

After applying these settings, the map visualization was complete, providing a clear and informative overview of the geographical distribution of failed RDP attempts.

![img](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/main/Picture1.png?raw=true)

The next picture is the number of API calls I had that day. All of them are not shown because I had to upgrade the number of calls I could make.

![image](https://github.com/Walikhan7866/SIEM-Implementation-in-Azure-Cloud-/blob/my-new-branch/siem2/api2.jpg?raw=true)


## Conclusions:
Through a comprehensive analysis of the monitoring system's performance, it has been determined that the Windows Event Viewer functions as expected, accurately logging all login attempts. However, the PowerShell script utilized for real-time monitoring presents a bottleneck, unable to keep pace with the frequency of login attempts under certain conditions. This bottleneck results in discrepancies between the recorded data in the PowerShell script and the Event Viewer logs.

The observed discrepancies stem from the script's limitation in recording login attempts at a rate that matches the actual frequency of occurrences. Setting a refresh rate of 300 milliseconds proved insufficient to capture all login attempts in real-time, leading to missed or delayed recordings.

To address this issue, potential solutions include lowering the refresh rate further to allow the script to capture a higher volume of login attempts per unit of time. Additionally, optimizing the efficiency of the script or exploring alternative monitoring methods could enhance the system's performance and reliability.

In conclusion, while the Windows Event Viewer reliably logs login attempts, improvements are needed in the real-time monitoring script to ensure accurate and timely recording of all events. By implementing the suggested optimizations and adjustments, the monitoring system can be enhanced to better meet the demands of effectively tracking and responding to security events.

