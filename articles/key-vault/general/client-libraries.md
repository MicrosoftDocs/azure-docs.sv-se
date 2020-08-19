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
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264093"
---
# <a name="client-libraries-for-azure-key-vault"></a>Klient bibliotek för Azure Key Vault

Klient biblioteken för Azure Key Vault ger program mässig åtkomst till Key Vault funktioner från flera olika språk, inklusive .NET, python, Java och Java Script.

## <a name="client-libraries-per-language-and-object"></a>Klient bibliotek per språk och objekt

Varje SDK har separata klient bibliotek för hemligheter, nycklar och certifikat enligt tabellen nedan.

| Språk | Hemligheter | Nycklar | Certifikat |
|--|--|--|--|
| .NET | - [API-referens](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Start](../secrets/quick-create-net.md) | - [API-referens](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API-referens](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet-paket](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API-referens](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Start](../secrets/quick-create-python.md) |- [API-referens](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-keys/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Start](../keys/quick-create-python.md) | - [API-referens](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi-paket](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Start](../certificates/quick-create-python.md) |
| Java | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Start](../secrets/quick-create-java.md) |- [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API-referens](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Start](../secrets/quick-create-node.md) |- [API-referens](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API-referens](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM-paket](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Nästa steg

- Se [guiden för Azure Key Vault utvecklare](developers-guide.md)
- Läs mer om [hanterad identitet för Azure Key Vault](managed-identity.md)