---
title: Klient bibliotek för Azure Key Vault | Microsoft Docs
description: Klient bibliotek för Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 0e03427da99b2011309fde2377efc3a571eac124
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492914"
---
# <a name="client-libraries-for-azure-key-vault"></a>Klient bibliotek för Azure Key Vault

Klient biblioteken för Azure Key Vault ger program mässig åtkomst till Key Vault funktioner från flera olika språk, inklusive .NET, python, Java och Java Script.

## <a name="client-libraries-per-language-and-object"></a>Klient bibliotek per språk och objekt

Varje SDK har separata klient bibliotek för nyckel valv, hemligheter, nycklar och certifikat, enligt tabellen nedan.

| Språk | Hemligheter | Nycklar | Certifikat | Key Vault (hanterings plan) |
|--|--|--|--|--|
| .NET | - [API-referens](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Start](../secrets/quick-create-net.md) | - [API-referens](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Start](../keys/quick-create-net.md) | - [API-referens](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Start](../certificates/quick-create-net.md) | - [API-referens](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API-referens](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Start](../secrets/quick-create-python.md) |- [API-referens](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Start](../keys/quick-create-python.md) | - [API-referens](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Start](../certificates/quick-create-python.md) | - [API-referens](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [PyPi-paket](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Start](../secrets/quick-create-java.md) |- [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Start](../keys/quick-create-java.md) | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Start](../certificates/quick-create-java.md) |- [API-referens](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API-referens](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Start](../secrets/quick-create-node.md) |- [API-referens](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Start](../keys/quick-create-node.md)| - [API-referens](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Start](../certificates/quick-create-node.md) |  - [API-referens](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Nästa steg

- Se [guiden för Azure Key Vault utvecklare](developers-guide.md)
- Läs mer om [att autentisera till ett nyckel valv](authentication.md)
- Läs mer om [att skydda åtkomsten till en Key Vault](secure-your-key-vault.md)
