---
title: IP addresses used by Application Insights | Microsoft Docs
description: Server firewall exceptions required by Application Insights
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm

ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: cfreeman

---
# IP addresses used by Application Insights
The [Azure Application Insights](app-insights-overview.md) service uses a number of IP addresses. You might need to know these addresses if the app that you are monitoring is hosted behind a firewall.

> [!NOTE]
> Although these addresses are static, it's possible that we will need to change them from time to time.
> 
> 

## Outgoing ports
You need to open some outgoing ports in your server's firewall to allow the Application Insights SDK and/or Status Monitor to send data to the portal:

| Purpose | URL | IP | Ports |
| --- | --- | --- | --- |
| Telemetry |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244 |443 |
| Live Metrics Stream |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |
| Internal Telemetry |breeze.aimon.applicationinsights.io |52.161.11.71 |443 |

## Status Monitor
Status Monitor Configuration - needed only when making changes.

| Purpose | URL | IP | Ports |
| --- | --- | --- | --- |
| Configuration |`management.core.windows.net` | |`443` |
| Configuration |`management.azure.com` | |`443` |
| Configuration |`login.windows.net` | |`443` |
| Configuration |`login.microsoftonline.com` | |`443` |
| Configuration |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configuration |`auth.gfx.ms` | |`443` |
| Configuration |`login.live.com` | |`443` |
| Installation |`packages.nuget.org` | |`443` |

## HockeyApp
| Purpose | URL | IP | Ports |
| --- | --- | --- | --- |
| Crash data |gate.hockeyapp.net |104.45.136.42 |80, 443 |

## Availability tests
This is the list of addresses from which [availability web tests](app-insights-monitor-web-app-availability.md) are run. If you want to run web tests on your app, but your web server is restricted to serving specific clients, then you will have to permit incoming traffic from our availability test servers.

Open ports 80 (http) and 443 (https) for incoming traffic from these addresses (IP addresses are grouped by location):

```
AU : Sydney
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
13.70.83.252
13.75.150.96
13.75.153.9
13.75.158.185
BR : Sao Paulo
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
191.232.32.122   
191.232.172.45   
191.232.176.218
191.232.191.225
CH : Zurich
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
FR : Paris
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.72.52
94.245.72.53
HK : Hong Kong
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
13.75.121.122
23.99.115.153
23.99.123.38
23.102.232.186
52.175.38.49
52.175.39.103
IE : Dublin
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
13.74.184.101
13.74.185.160
40.69.200.198
52.164.224.46
52.169.12.203
52.169.14.11
52.169.237.149
52.178.183.105
JP : Kawaguchi
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
52.243.33.33
52.243.33.141
52.243.35.253
52.243.41.117
NL : Amsterdam
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
52.174.166.113
52.174.178.96
52.174.31.140
52.174.35.14
52.178.104.23
52.178.109.190
52.178.111.139
52.233.166.221
RU : Moscow
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
SE : Stockholm
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
SG : Singapore
52.187.29.7 
52.187.179.17 
52.187.76.248 
52.187.43.24 
52.163.57.91 
52.187.30.120 
US : CA-San Jose
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.169
207.46.98.170
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
104.45.228.236
104.45.237.251
13.64.152.110
13.64.156.54
13.64.232.251
13.64.236.105
13.91.94.59
40.118.131.182
40.83.189.192
40.83.215.122
US : FL-Miami
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.54.78.59
65.54.78.60
US : IL-Chicago
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
207.46.14.67
207.46.14.68
23.96.247.139
23.96.249.113
52.162.124.242
52.162.126.139
52.162.241.147
52.162.246.222
52.162.247.136
52.237.153.231
52.237.154.216
52.237.156.14
52.237.157.218
52.237.157.37
US : TX-San Antonio
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.77
65.55.82.78
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
104.210.145.106
13.84.176.24
13.84.49.16
13.85.11.137
13.85.26.248
13.85.69.145
52.171.136.162
52.171.141.253
52.171.57.172
52.171.58.140
US : VA-Ashburn
13.106.106.20
13.106.106.21
13.106.106.22
13.106.106.23
13.106.106.24
13.106.106.25
13.106.106.26
13.106.106.27
13.106.106.28
13.106.106.29
13.82.218.95
13.90.96.71
13.90.98.52
13.92.137.70
40.85.187.235
40.87.61.61
52.168.8.247
52.170.38.79
52.170.80.61
52.179.9.26

```  

## Data access API
| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| API docs |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |
| Internal API |aigs.aisvc.visualstudio.com<br/>aigs1.aisvc.visualstudio.com<br/>aigs2.aisvc.visualstudio.com<br/>aigs3.aisvc.visualstudio.com<br/>aigs4.aisvc.visualstudio.com<br/>aigs5.aisvc.visualstudio.com<br/>aigs6.aisvc.visualstudio.com |dynamic|443 |

## Application Insights Analytics

| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| Analytics Portal | analytics.applicationinsights.io | dynamic | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamic | 80,443 |
| Media CDN | applicationanalyticsmedia.azureedge.net | dynamic | 80,443 |

Note: *.applicationinsights.io domain is owned by Application Insights team.

## Application Insights Azure Portal Extension

| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| Application Insights Extension | stamp2.app.insightsportal.visualstudio.com | dynamic | 80,443 |
| Application Insights Extension CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamic | 80,443 |

## Application Insights SDKs

| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | dynamic | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | dynamic | 80,443 |

## Profiler

| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | dynamic | 443
| Portal | gateway.azureserviceprofiler.net | dynamic | 443
| Storage | *.core.windows.net | dynamic | 443

## Snapshot Debugger

| Purpose | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | dynamic | 443
| Portal | ppe.gateway.azureserviceprofiler.net | dynamic | 443
| Storage | *.core.windows.net | dynamic | 443
