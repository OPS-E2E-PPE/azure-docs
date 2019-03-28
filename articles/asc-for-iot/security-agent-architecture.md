---
title: Understanding ASC for IoT security agent architecture Preview| Microsoft Docs
description: Understand security agent architecture for the agents used in the ASC for IoT service.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''

ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner

---
# Security agent reference architecture

> [!IMPORTANT]
> ASC for IoT is currently in public preview.
> This preview version is provided without a service level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC for IoT provides reference architecture for security agents that log, process, aggregate, and send security data through IoT hub.

Security agents are designed to work in a constrained IoT environment, and are highly customizable in terms of values they provide when compared to the resources they consume.

Security agents support the following IoT solution features:

- Collect raw security events from the underlying OS (Linux, Windows). To learn more about available security data collectors, see [ASC for IoT agent configuration](concept-agent-configuration.md).

- Aggregate raw security events into messages sent through IoT hub.

- Authenticate with existing device identity, or a dedicated module identity. See [Security agent authentication methods](concept-security-agent-authentication-methods.md) to learn more.

- Configure remotely through use of the **ascforiot** module twin. To learn more, see [Configure an ASC for IoT agent](concept-agent-configuration.md).

ASC for IoT Security agents are developed as open-source projects, and are available from GitHub: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## Agent supported platforms

ASC for IoT offers different installer agents for 32bit and 64bit Windows, and the same for 32bit and 64bit Linux. Make sure you have the correct agent installer for each of your devices according to the following table:

| 32 or 64bit | Linux | Windows |    Details|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C# or C           | C#      | Use the C agent for devices with minimal resources|

## Next steps

In this article, you learned about ASC for IoT security agent architecture, and the available installers.

To continue getting started with ASC for IoT deployment, use the following articles:


- Review the ASC for IoT [Service prerequisites](service-prerequisites.md)
- Learn how to [Enable ASC for IoT service in your IoT Hub](quickstart-onboard-iot-hub.md)
- Use the quickstart to [Configure your solution](quickstart-configure-your-solution.md)
- Understand [Security agent authentication methods](concept-security-agent-authentication-methods.md)
- Select and deploy a [security agent](select-deploy-agent.md)
- Learn more about the service from the [ASC for IoT FAQ](resources-frequently-asked-questions.md)