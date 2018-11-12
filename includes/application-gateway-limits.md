---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
---
| Resource | Default limit | Note |
| --- | --- | --- |
| Application Gateway |1000 per subscription | |
| Frontend IP Configurations |2 |1 public and 1 private |
| Frontend Ports |100 | |
| Backend Address Pools |100 | |
| Backend Servers per pool |100 | |
| HTTP Listeners |100 | |
| HTTP load balancing rules |200 |# of HTTP Listeners * n |
| Backend HTTP settings |100 | |
| Instances per gateway |75 | |
| SSL certificates |100 |1 per HTTP Listeners |
| Authentication certificates |100 | |
| Request time out min |1 second | |
| Request time out max |24 hrs | |
| Number of sites |20 |1 per HTTP Listeners |
| URL Maps per listener |1 | |
|Maximum path-based rules per URL map|100|
| Concurrent WebSocket Connections |5000| |
|Maximum URL length|8000|
| Maximum file upload size Standard |2 GB | |
| Maximum file upload size WAF |Medium WAF Gateways - 100 MB<br>Large WAF Gateways - 500 MB| |
|WAF body size limit (without files)|128 KB|
