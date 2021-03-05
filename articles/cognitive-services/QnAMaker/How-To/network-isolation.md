---
title: Nätverksisolering
description: Användare kan begränsa offentlig åtkomst till QnA Maker resurser.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125092"
---
# <a name="recommended-settings-for-network-isolation"></a>Rekommenderade inställningar för nätverks isolering

Följ stegen nedan för att begränsa den offentliga åtkomsten till QnA Maker resurser. Skydda en Cognitive Services resurs från offentlig åtkomst genom [att konfigurera det virtuella nätverket](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Begränsa åtkomsten till Kognitiv sökning resurs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Konfigurera Kognitiv sökning som en privat slut punkt i ett VNET. När en Sök instans skapas när en QnA Maker resurs skapas kan du tvinga Kognitiv sökning till att stödja en privat slut punkts konfiguration som skapats helt i en kunds VNet.

Alla resurser måste skapas i samma region för att en privat slut punkt ska kunna användas.

* QnA Maker resurs
* ny Kognitiv sökning resurs
* ny Virtual Network resurs

Utför följande steg i [Azure Portal](https://portal.azure.com):

1. Skapa en [QNA Maker-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Skapa en ny Kognitiv sökning resurs med slut punkts anslutning (data) inställt på _privat_. Skapa resursen i samma region som QnA Maker resursen som skapades i steg 1. Lär dig mer om att [skapa en kognitiv sökning resurs](../../../search/search-create-service-portal.md)och Använd sedan den här länken för att gå direkt till [sidan Skapa i resursen](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Skapa en ny [Virtual Network-resurs](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Konfigurera VNET på App Service-resursen som skapades i steg 1 i den här proceduren. Skapa en ny DNS-post i VNET för ny Kognitiv sökning-resurs som skapades i steg 2. till Kognitiv sökning IP-adress.
5. [Koppla app service till den nya kognitiv sökning-resurs som](../how-to/set-up-qnamaker-service-azure.md) skapades i steg 2. Sedan kan du ta bort den ursprungliga Kognitiv sökning-resursen som skapades i steg 1.
    
Skapa din första kunskaps bas i [QNA Maker-portalen](https://www.qnamaker.ai/).

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

[Skapa privata slut punkter](../reference-private-endpoint.md) till Azure Search resursen.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Begränsa åtkomsten till App Service (QnA Runtime)

Du kan lägga till IP-adresser i App Service-tillåten för att begränsa åtkomsten eller konfigurera App Service environemnt till värd QnA Maker App Service.

#### <a name="add-ips-to-app-service-allowlist"></a>Lägg till IP-adresser i App Service tillåten

1. Tillåt endast trafik från Cognitive Services IP-adresser. Dessa ingår redan i Service Tag-numret `CognitiveServicesManagement` . Detta krävs för att skapa API: er (Skapa/uppdatera KB) för att anropa app service och uppdatera Azure Search-tjänsten enligt detta. Läs [Mer information om service märken.](../../../virtual-network/service-tags-overview.md)
2. Se till att du även tillåter andra start punkter som Azure Bot Service, QnA Maker Portal osv. för förutsägelse "GenerateAnswer" API-åtkomst.
3. Följ dessa steg om du vill lägga till IP-adressintervall till en tillåten:

   1. Hämta [IP-intervall för alla tjänst Taggar](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Välj IP-adresserna för "CognitiveServicesManagement".
   3. Gå till avsnittet nätverk i App Service resursen och klicka på alternativet för att konfigurera åtkomst begränsning för att lägga till IP-adresser i en tillåten.

Vi har också ett automatiserat skript som gör samma sak för din App Service. Du kan hitta [PowerShell-skriptet för att konfigurera en tillåten](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) på GitHub. Du måste skriva in prenumerations-ID, resurs grupp och faktiskt App Service namn som skript parametrar. Om du kör skriptet läggs IP-adresserna automatiskt till App Service tillåten.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurera App Service-miljön som värd QnA Maker App Service
    
App Service-miljön (ASE) kan användas för att vara värd för QnA Maker app service. Följ stegen nedan:

1. Skapa en App Service-miljön och markera den som "extern". Följ [själv studie kursen](../../../app-service/environment/create-external-ase.md) om du vill ha mer information.
2.  Skapa en app service i App Service-miljön.
    1. Kontrol lera konfigurationen för app service och Lägg till "PrimaryEndpointKey" som en program inställning. Värdet för ' PrimaryEndpointKey ' ska vara inställt på " \<app-name\> -PrimaryEndpointKey". Appens namn definieras i App Service-URL: en. Om App Service-URL: en till exempel är "mywebsite.myase.p.azurewebsite.net" är App-Name "min webbplats". I det här fallet ska värdet för ' PrimaryEndpointKey ' anges till "Website-PrimaryEndpointKey".
    2. Skapa en Azure Search-tjänst.
    3. Se till att Azure Search-och appinställningar har kon figurer ATS korrekt. 
          Följ den här [självstudien](../reference-app-service.md?tabs=v1#app-service).
3.  Uppdatera nätverks säkerhets gruppen som är kopplad till App Service-miljön
    1. Uppdatera i förväg skapade inkommande säkerhets regler enligt dina krav.
    2. Lägg till en ny inkommande säkerhets regel med källan som service tag och käll tjänst tag gen som ' CognitiveServicesManagement '.
4.  Skapa en QnA Maker kognitiv tjänst instans (Microsoft. CognitiveServices/Accounts) med Azure Resource Manager där QnA Maker-slutpunkten ska ställas in på App Service slut punkten som skapades ovan (https://mywebsite.myase.p.azurewebsite.net).
    
---
