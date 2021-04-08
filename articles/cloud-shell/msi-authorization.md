---
title: Använda hanterade identiteter för resurser i Azure Cloud Shell
description: Autentisera kod med MSI i Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469906"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Använda hanterade identiteter för Azure-resurser i Azure Cloud Shell

Azure Cloud Shell stöder auktorisering med hanterade identiteter för Azure-resurser. Använd detta för att hämta åtkomsttoken för att kommunicera säkert med Azure-tjänster.

## <a name="about-managed-identities-for-azure-resources"></a>Om hanterade identiteter för Azure-resurser
En vanlig utmaning när du bygger moln program är att på ett säkert sätt hantera de autentiseringsuppgifter som måste finnas i din kod för att autentisera till moln tjänster. I Cloud Shell kan du behöva autentisera hämtning från Key Vault för att få autentiseringsuppgifter som ett skript kan behöva.

Hanterade identiteter för Azure-resurser gör det enklare att lösa det här problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

## <a name="acquire-access-token-in-cloud-shell"></a>Hämta åtkomsttoken i Cloud Shell

Kör följande kommandon för att ställa in din MSI-åtkomsttoken som en miljö variabel `access_token` .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Förfallo datum för hantering av token

Det lokala MSI-undersystemet cachelagrar tokens. Därför kan du anropa det så ofta du vill, och ett anrop till Azure AD uppstår endast om:
- det uppstår ett cacheminne på grund av ingen token i cachen
- token har upphört att gälla

Om du cachelagrar token i koden bör du vara beredd på att hantera scenarier där resursen indikerar att token har upphört att gälla.

Om du vill hantera token-fel går du till [MSI-sidan om hur du svänger MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling)-åtkomsttoken.

## <a name="next-steps"></a>Nästa steg
[Läs mer om MSI](../active-directory/managed-identities-azure-resources/overview.md)  
[Hämta åtkomsttoken från virtuella MSI-datorer](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)