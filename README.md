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

## Setting the "correct" Azure Policies to install AMA on ARC machines

First thing you will try is going to Azure Policy page and search for policies that install AMA on ARC machines. However, this apparently simple task is not so simple, as you will find over 38 policies (in the time this article was written - July 2023) that mention ARC, Hybrid and AMA or Azure Monitor Agent in the policy name.

Here it is where this article may help you. Among those 38 policies, the two policies below WILL really INSTALL the AMA (not the MMA - legacy one) in your brand new added ARC machines on Azure:

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
Policy to only audit if AMA doesn't exist on ARC machines

## Checking if AMA and others extension is installed on ARC machines

An easy way to check if your AMA installation was successful is going to "Azure ARC | Servers" page, select the ARC machine you want to check, go to "Extensions" in the left menu and check for the extensions installed and see if the status says "Success". 

<img width="1178" alt="image" src="https://github.com/ruolivei/Azure-Police-For-ARC-Servers/assets/16666958/5fd5c973-6944-4e08-9655-e46a495079fa">

In the above screen you may check some of the extensions installed through Azure policies automated deployment.
