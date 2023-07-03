# Azure-Police-For-ARC-Servers
What Azure Policies you can use to automate Azure Monitor Agent installation on ARC Servers

## Quick Intro

Azure ARC for Servers is regarding adding an Agent on Non-Azure VMs and physical servers. Once that agent is installed on those VMs, they will "appear" on your Azure Portal under "Azure ARC | Servers".

This bring some benefits to your non-Azure (on-premises, AWS, GCP, others DCs) VMs. They are:

- Azure automanage (VMs best practices on Azure. it still has some limitations for ARC machines)
- automatic onboard on MDC (Microsoft Defender for Cloud)
- consequently, MDC will onboard those ARC machines on MDE (Microsoft Defender for Endpoint) ... you will need to check for OS compatibility
- usage of Azure Automation
- usage of Update Management service
- usage of "some" Azure Policies on those ARC machines
- one of the most common usage, ARC machines will be able to ingest logs on Log Analytics and Microsoft Sentinel

## Common steps

Once you install ARC agent, as mentioned above, VMs will appear in your Azure environment.

A very common second step is about installing Azure Monitor Agent (AMA) on those machines, so you may ingest Security logs into Log Analytics and Sentinel.
You may accomplish this task at scale in two ways:

1. configuring a DCR(Data Collection Rule)
    - under Azure Monitor service (logs goes to LAW table named "Event")
    - under Microsoft Sentinel Data Connectors (logs goes to LAW table named "SecurityEvent")

2. using Azure Policies

Two main policies
<img width="1082" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/aae0dba5-4d13-487f-b15e-ceeb814a250c">

Adding VM Insights and Service Map
<img width="1081" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/df79a86c-81b0-43df-9aee-d9ef94e49c6c">

Policy to only audit if AMA doesn't exist on ARC machines
<img width="1067" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/e762741c-4ca4-4473-bf09-3f13aa49b235">


ARC machines extensions to be verified
<img width="1178" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/5fd5c973-6944-4e08-9655-e46a495079fa">
