# Azure-Police-For-ARC-Servers
What Azure Policies can you use to automate Azure Monitor Agent installation on ARC Servers?

## Quick Intro

Azure ARC for Servers is regarding adding an Agent on Non-Azure VMs and physical servers. Once that agent is installed on those VMs, they will "appear" on your Azure Portal under "Azure ARC | Servers."

This bring some benefits to your non-Azure (on-premises, AWS, GCP, other DCs) VMs. They are:

- Azure automanage (VMs best practices on Azure. It still has some limitations for ARC machines)
- automatic onboard on MDC (Microsoft Defender for Cloud)
- consequently, MDC will onboard those ARC machines on MDE (Microsoft Defender for Endpoint) ... you will need to check for OS compatibility
- use of Azure Automation
- Use of Update Management service
- use of "some" Azure Policies on those ARC machines
- one of the most common uses, ARC machines will be able to ingest logs on Log Analytics and Microsoft Sentinel

## Common steps

Once you install the ARC agent, as mentioned above, VMs will appear in your Azure environment.

A very common second step is about installing Azure Monitor Agent (AMA) on those machines, so you may ingest Security logs into Log Analytics and Sentinel.
You may accomplish this task at scale in two ways:

1. configuring a DCR(Data Collection Rule)
    - under Azure Monitor service (logs goes to LAW table named "Event")
    - under Microsoft Sentinel Data Connectors (logs goes to LAW table named "SecurityEvent")

2. using Azure Policies

## Setting the "correct" Azure Policies to install AMA on ARC machines

First thing you will try is going to Azure Policy page and searching for policies that install AMA on ARC machines. However, this simple task is more complex, as you will find over 38 policies (when this article was written - July 2023) that mention ARC, Hybrid and AMA or Azure Monitor Agent in the policy name.

Here it is where this article may help you. Among those 38 policies, the two policies below WILL INSTALL the AMA (not the MMA - legacy one) in your brand new added ARC machines on Azure:

<img width="1082" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/aae0dba5-4d13-487f-b15e-ceeb814a250c">

### NOTEs:

- the policy effect is "deploy if not exists", that confirm that AMA will be installed.
- because of the effect "deploy ...", you will have to specify during policy configuration, that you want to use a "Managed Identity".
- the reason you see two policies is because:
    - the first policy "only" install the agent
    - AMA agents mandatorily requires a DCR, so you will have to previously create a DCR and take note of its resource ID, so you will use with the second policy above.

## adding VM insights and Service Map

In case you want to add VM insight metrics and service map (port and services mapping) to your ARC machines, you may also consider this policy below. It also has the effect "deploy if doesnt' exist).

<img width="1081" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/df79a86c-81b0-43df-9aee-d9ef94e49c6c">

## Only auditing (not deploy)

in case you decide to use DCR to deploy AMA at scale and not use Azure Policies to "deploy" the AMA, you may consider a policy to only "audit" and let you know what ARC machines didn't have the AMA installed.

<img width="1067" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/e762741c-4ca4-4473-bf09-3f13aa49b235">

## Checking if AMA and others extension is installed on ARC machines

An easy way to check if your AMA installation was successful is going to "Azure ARC | Servers" page, select the ARC machine you want to check, go to "Extensions" in the left menu and check for the extensions installed and see if the status says "Success". 

In the example below, I have used three policies to deploy AMA and VM Insight on ARC machines. Remember that AMA installation require two policies, one to install AMA and another to associate the AMA with a DCR. The third policy is to install the VM insight and Service Map:

<img width="1078" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/86312ac4-cfe2-4afd-9f7d-289268126092">

Those were the agents installed under extension, so you may check them out:

- DependencyAgentWindows
- AzureMonitorWindowsAgent
- AzureSecurityWindowsAgent
- MDE.Windows

<img width="1187" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/b54b6cfb-a1f0-4570-97cf-15bbe57fbb36">

### DependencyAgentWindows

This agent is installed due to the policy "[Preview]: Configure Dependency agent on Azure Arc enabled Windows servers with Azure Monitoring Agent settings". It will collect VM insights metrics and provide ARC machines with a Service and ports mapping under Azure Monitor.

### AzureMonitorWindowsAgent

This is the AMA (Azure Monitor Agent) installed through the other two policies:

- Configure Windows Arc-enabled machines to run Azure Monitor Agent
- Configure Windows Arc Machines to be associated with a Data Collection Rule or a Data Collection Endpoint (this one associate the agent with a DCR and allow the ARC machine to collect logs according to DCR configuration)

### AzureSecurityWindowsAgent

This is the MDC (Microsoft Defender for Cloud) agent, installed automatically once the ARC machine has the ARC agent successfully installed and it showed on Azure ARC for servers page.

### MDE.Windows

Once your machine is onboarded on MDC, you have the Defender for Servers (under MDC) configured with the paid version (Plan 2), AND, you have a VM Operating System version compatible with MDE, then, your ARC machine will be onboarded automatically on MDE (Microsoft Defender for Endpoint). That is the reason you will have this agent under extension and that is good way to confirm that your ARC machine is onboarded on MDE.
