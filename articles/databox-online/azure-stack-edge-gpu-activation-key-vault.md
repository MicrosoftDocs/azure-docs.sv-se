---
title: Integrering av Azure Key Vault med Azure Stack Edge-resurs och enhets aktivering
description: Beskriver hur Azure Key Vault är kopplad till hemlig hantering under aktiveringen av Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439875"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault integration med Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault är integrerat med Azure Stack Edge-resurs för hemlig hantering. Den här artikeln innehåller information om hur en Azure Key Vault skapas för Azure Stack Edge-resurs under enhets aktivering och sedan används för hemlig hantering. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Om Key Vault och Azure Stack Edge

Azure Key Vault moln tjänst används för säker lagring och kontroll av åtkomst till token, lösen ord, certifikat, API-nycklar och andra hemligheter. Key Vault gör det också enkelt att skapa och kontrol lera de krypterings nycklar som används för att kryptera dina data. Mer information om tillåtna transaktioner och motsvarande kostnader finns i [prissättning för Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

En av de hemligheter som används för tjänsten Azure Stack Edge är kanal integritets nyckel (CIK). Med den här nyckeln kan du kryptera dina hemligheter. Med integreringen av Key Vault lagras CIK säkert i nyckel valvet. Mer information finns i [säker lagring av hemligheter och nycklar](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Skapa nyckel valv

Ett nyckel valv skapas för Azure Stack Edge-resurs under genereringen av aktiverings nyckeln. 

- När du skapar Azure Stack Edge-resurs måste du registrera *Microsoft. Vault* -resurs leverantören. Resurs leverantören registreras automatiskt om du har ägar-eller deltagar åtkomst till prenumerationen. Nyckel valvet skapas i samma prenumeration och resurs gruppen som Azure Stack Edge-resurs. 

- När du skapar en Azure Stack Edge-resurs skapas även en Hanterad tjänstidentitet (MSI) som är kvar för resursens livs längd och kommunicerar med resurs leverantören i molnet. 

    När MSI är aktiverat skapar Azure en betrodd identitet för Azure Stack Edge-resursen.

- När du har skapat Azure Stack Edge-resursen och du genererar en aktiverings nyckel från Azure Portal skapas ett nyckel valv. Det här nyckel valvet används för hemliga hantering och finns kvar så länge som Azure Stack Edge-resursen finns. 

    ![Key Vault skapas under genereringen av aktiverings nyckeln](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Du kan välja att godkänna standard nyckel namnet eller ange ett eget namn för nyckel valvet. Nyckel valvets namn måste vara mellan 3 och 24 tecken långt. Det går inte att använda ett nyckel valv som redan används. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![MSI skapade när Azure Stack Edge-resurs skapas](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Om du vill bläddra till Azure Key Vault går du till **egenskaperna** i Azure Stack Edge-resursen och väljer nyckel valvets namn. 

- För att förhindra oavsiktlig borttagning är ett resurs lås aktiverat i nyckel valvet. En mjuk borttagning är också aktive rad i nyckel valvet som gör att nyckel valvet kan återställas inom 90 dagar om det uppstår en oavsiktlig borttagning. Mer information finns i [Azure Key Vault översikt över mjuk borttagning](../key-vault/general/soft-delete-overview.md)

    Om nyckel valvet har tagits bort av misstag och varaktigheten för rensnings skyddet på 90 dagar inte har gått ut, följer du dessa steg för att [återställa nyckel valvet](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). 

- Om du har en befintlig Azure Stack Edge-resurs innan Azure Key Vault hade integrerats med Azure Stack Edge-resurs påverkas inte du. Du kan fortsätta att använda din befintliga Azure Stack Edge-resurs. 

- När Azure Stack Edge-resursen tas bort, tas Azure Key Vault också bort med resursen. Du uppmanas att bekräfta. Om du inte tänker ta bort det här nyckel valvet kan du välja att inte bevilja medgivande. Det är bara Azure Stack Edge-resursen som tas bort som lämnar nyckel valvet intakt. 

- Om det här nyckel valvet användes för att lagra andra nycklar kan du fortfarande återställa det inom 90 dagar från borttagningen. Under den här rensnings skydds perioden kan Key Vault-namnet inte användas för att skapa ett nytt nyckel valv.

Om du stöter på problem som rör Key Vault och enhets aktivering läser du [Felsök problem med enhets aktivering](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [genererar aktiverings nyckeln](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).