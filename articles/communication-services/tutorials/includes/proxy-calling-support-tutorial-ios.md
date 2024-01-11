---
title: include file
description: include file
author: sloanster
services: azure-communication-services
ms.date: 08/14/2023
ms.topic: include
ms.author: chengyuanlai
ms.service: azure-communication-services
ms.subservice: calling
ms.custom: mode-other
---

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

>[!IMPORTANT]
> The proxy feature will NOT be available for Teams Identities and Azure Communication Services Teams interop actions.

## Proxy calling media traffic

### What is a TURN server?
Many times, establishing a network connection between two peers isn't straightforward. A direct connection might not work because of many reasons: firewalls with strict rules, peers sitting behind a private network, or computers running in a NAT (Network Address Translation) environment. To solve these network connection issues, you can use a TURN server. The term stands for Traversal Using Relays around NAT, and it's a protocol for relaying network traffic. STUN and TURN servers are the relay servers here. [Learn more about how Azure Communication Services mitigates network challenges by utilizing STUN and TURN](../../concepts/network-traversal.md).

### Provide your TURN server details with the SDK
To provide the details of your TURN servers, you need to pass details of what TURN server to use as part of `CallClientOptions` while initializing the `CallClient`. For more information how to set up a call, see [Azure Communication Services iOS SDK](../../quickstarts/voice-video-calling/get-started-with-video-calling.md?pivots=platform-ios) for the Quickstart on how to setup Voice and Video.

```swift
let callClientOptions = new CallClientOptions()
let callNetworkOptions = new CallNetworkOptions()

let iceServer = IceServer()
iceServer.urls = ["turn:20.202.255.255"]
iceServer.udpPort = 3478
iceServer.realm = "turn.azure.com"
iceServer.username = "turnserver1username"
iceServer.password = "turnserver1password"

callNetworkOptions.iceServers = [iceServer]

// Supply the network options when creating an instance of the CallClient
callClientOptions.network = callNetworkOptions
self.callClient = CallClient(options: callClientOptions);

// ...continue normally with your SDK setup and usage.
```

> [!IMPORTANT]
> Note that if you have provided your TURN server details while initializing the `CallClient`, all the media traffic will <i>exclusively</i> flow through these TURN servers. Any other ICE candidates that are normally generated when creating a call won't be considered while trying to establish connectivity between peers i.e. only 'relay' candidates will be considered. To learn more about different types of Ice candidates click [here](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidate/type).

> [!NOTE]
> Currently, iOS SDK only supports <b>one IPv4 address</b> and <b>UDP</b> protocol for media proxy. Any URLs in non-ipv4 format will be ignored. When multiple URLs are provided, only the last one will be used by the SDK.
> If UDP port is not provided, a default UDP port 3478 will be used. 

> [!NOTE]
> If any of the URLs provided are invalid, the `CallClient` initialization will fail and will throw errors accordingly.

### Set up a TURN server in Azure
You can create a Linux virtual machine in the Azure portal using this [guide](/azure/virtual-machines/linux/quick-create-portal?tabs=ubuntu) and deploy a TURN server using [coturn](https://github.com/coturn/coturn). Coturn is a free and open source implementation of a TURN and STUN server for VoIP and WebRTC.

Once you have setup a TURN server, you can test it using the WebRTC Trickle ICE page - [Trickle ICE](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/).

## Proxy signaling traffic

To provide the URL of a proxy server, you need to pass it in as part of `CallClientOptions` through its property `Network` while initializing the `CallClient`. For more information on how to set up a call, see [Azure Communication Services iOS SDK](../../quickstarts/voice-video-calling/get-started-with-video-calling.md?pivots=platform-ios) for the Quickstart on how to setup Voice and Video.

```swift
let callClientOptions = CallClientOptions()
let callNetworkOptions = CallNetworkOptions()
callNetworkOptions.proxyUrl = proxyUrl
callClientOptions.network = callNetworkOptions
self.callClient = CallClient(options: callClientOptions)

// ...continue normally with your SDK setup and usage.
```

### Setting up a signaling proxy server on Azure
You can create a Linux virtual machine in the Azure portal and deploy an NGINX server on it using this guide - [Quickstart: Create a Linux virtual machine in the Azure portal](/azure/virtual-machines/linux/quick-create-portal?tabs=ubuntu).

Here's an NGINX config that you could make use of for a quick spin up:
```
events {
    multi_accept       on;
    worker_connections 65535;
}
http {
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }
    map $request_method $access_control_header {
        OPTIONS '*';
    }
    server {
        listen <port_you_want_listen_on> ssl;
        ssl_certificate     <path_to_your_ssl_cert>;
        ssl_certificate_key <path_to_your_ssl_key>;
        location ~* ^/(.*?\.(com|net)(?::[\d]+)?)/(.*)$ {
            if ($request_method = 'OPTIONS') {
                add_header Access-Control-Allow-Origin '*' always;
                add_header Access-Control-Allow-Credentials 'true' always;
                add_header Access-Control-Allow-Headers '*' always;
                add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
                add_header Access-Control-Max-Age 1728000;
                add_header Content-Type 'text/plain';
                add_header Content-Length 0;
                return 204;
            }
            resolver 1.1.1.1;
            set $ups_host $1;
            set $r_uri $3;
            rewrite ^/.*$ /$r_uri break;
            proxy_set_header Host $ups_host;
            proxy_ssl_server_name on;
            proxy_ssl_protocols TLSv1.2;
            proxy_ssl_ciphers DEFAULT;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass_header Authorization;
            proxy_pass_request_headers on;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_set_header Proxy "";
            proxy_set_header Access-Control-Allow-Origin $access_control_header;
            proxy_pass https://$ups_host;
            proxy_redirect https://$ups_host https://$host/$ups_host;
            proxy_intercept_errors on;
            error_page 301 302 307 = @process_redirect;
            error_page 400 405 = @process_error_response;
        }
        location @process_redirect {
            set $saved_redirect_location '$upstream_http_location';
            resolver 1.1.1.1;
            proxy_pass $saved_redirect_location;
            add_header X-DBUG-MSG "301" always;
        }
        location @process_error_response {
            add_header Access-Control-Allow-Origin * always;
        }
    }
}
```