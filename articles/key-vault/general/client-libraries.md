---
title: Klientbibliotek för Azure Key Vault | Microsoft Docs
description: Klientbibliotek för Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: b8690b5ce3e2aee9a554d3e0682161b86264e124
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818541"
---
# <a name="client-libraries-for-azure-key-vault"></a>Klientbibliotek för Azure Key Vault

Klientbiblioteken för Azure Key Vault programmatisk åtkomst till Key Vault funktioner från en mängd olika språk, inklusive .NET, Python, Java och JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Klientbibliotek per språk och objekt

Varje SDK har separata klientbibliotek för nyckelvalv, hemligheter, nycklar och certifikat enligt tabellen nedan.

| Språk | Hemligheter | Nycklar | Certifikat | Key Vault (hanteringsplanet) |
|--|--|--|--|--|
| .NET | - [API-referens](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Quickstart](../secrets/quick-create-net.md) | - [API-referens](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Quickstart](../keys/quick-create-net.md) | - [API-referens](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Quickstart](../certificates/quick-create-net.md) | - [API-referens](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-referens](/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-python.md) |- [API-referens](/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Quickstart](../keys/quick-create-python.md) | - [API-referens](/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-python.md) | - [API-referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [PyPi-paket](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-java.md) |- [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Quickstart](../keys/quick-create-java.md) | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-java.md) |- [API-referens](/java/api/com.microsoft.azure.management.keyvault)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API-referens](/javascript/api/@azure/keyvault-secrets/)<br>- [npm-paket](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-node.md) |- [API-referens](/javascript/api/@azure/keyvault-keys/)<br>- [npm-paket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Quickstart](../keys/quick-create-node.md)| - [API-referens](/javascript/api/@azure/keyvault-certificates/)<br>- [npm-paket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-node.md) |  - [API-referens](/javascript/api/@azure/arm-keyvault/)<br>- [npm-paket](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Nästa steg

- Se guiden [Azure Key Vault utvecklare](developers-guide.md)
- Läs mer om [autentisering till ett nyckelvalv](authentication.md)
- Läs mer om [att skydda åtkomst till en Key Vault](security-features.md)
