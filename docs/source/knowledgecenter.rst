Knowledge center
========================


Centralized Agent Management
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
For many of the compliance controls, we are required to install a few agent based softwares in each VM that is in scope. A few examples are Wazuh agent to fetch all the logs, ClamAV virus scanner, AWS Inspector that provides vulnerability scanning and Cloudwatch agent for host metrics. While these 4 agents are installed by default,. DuploCloud provides a framework where the user can specify an arbitrary list of agents in the following format and DuploCloud will install these automatically in any VM launched. If any of these agents crash then DuploCloud will send an alert. One good use case is to monitor the health of clamAV agent.

In DuploCloud UI this configuration is under Security → Agents Tab

::

	[
			{
					"AgentName":"AwsAgent",
					"AgentWindowsPackagePath":"https://inspector-agent.amazonaws.com/windows/installer/latest/AWSAgentInstall.exe",
					"AgentLinuxPackagePath":"https://inspector-agent.amazonaws.com/linux/latest/install",
					"LinuxAgentInstallStatusCmd":"sudo service --status-all | grep -wc 'awsagent'",                
					"WindowsAgentServiceName":"awsagent",
					"LinuxAgentServiceName":"awsagent",
					"LinuxInstallCmd":"sudo bash install"
			},
			{
					"AgentName":"ClamAV_v0",
					"AgentWindowsPackagePath":"",
					"LinuxAgentInstallStatusCmd":"sudo service clamav-freshclam status | grep -wc 'running'",                
					"AgentLinuxPackagePath":"https://www.google.com",
					"WindowsAgentServiceName":"",
					"LinuxAgentServiceName":"clamav-freshclam",
					"LinuxInstallCmd":"OS_FAMILY=$(cat /etc/os-release | grep PRETTY_NAME); if [[ $OS_FAMILY == *'Ubuntu'* ]]; then sudo apt-get update; sudo apt-get install -y clamav; else sudo amazon-linux-extras install -y epel; sudo yum install clamav clamd -y; sudo service clamav-freshclam start; fi",
					"LinuxAgentUninstallStatusCmd":"OS_FAMILY=$(cat /etc/os-release | grep PRETTY_NAME); if [[ $OS_FAMILY == *'Ubuntu'* ]]; then sudo apt-get autoremove -y --purge clamav; else sudo yum remove -y clamav*; fi"
			},
			{
					"AgentName": "clamav_scanner_v2",
					"AgentWindowsPackagePath": "",
					"AgentLinuxPackagePath": "https://www.google.com",
					"WindowsAgentServiceName": "",
					"LinuxAgentServiceName": "clamav-freshclam",
					"LinuxInstallCmd": "sudo unlink /etc/cron.hourly/clamscan_*; sudo wget -O installclamavcron.sh https://raw.githubusercontent.com/duplocloud/compliance/master/installclamavcron.sh; sudo chmod 0755 installclamavcron.sh; sudo ./installclamavcron.sh",
					"LinuxAgentInstallStatusCmd": "ls -la /etc/cron.hourly | grep -wc 'clamscan_v1_hourly'",
					"LinuxAgentUninstallStatusCmd": "unlink /etc/cron.hourly/clamscan_v1_hourly"
			}
	]

Deployment of simple architecture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
