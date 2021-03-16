---
title: Nätverksisolering
description: Användare kan begränsa offentlig åtkomst till QnA Maker resurser.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467552"
---
# <a name="recommended-settings-for-network-isolation"></a>Rekommenderade inställningar för nätverks isolering

Du bör följa stegen nedan för att begränsa den offentliga åtkomsten till QnA Maker resurser. Skydda en Cognitive Services resurs från offentlig åtkomst genom [att konfigurera det virtuella nätverket](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Begränsa åtkomsten till App Service (QnA Runtime)

Du kan lägga till IP-adresser i listan över tillåtna appar för att begränsa åtkomsten eller konfigurera App Service-miljön till värd QnA Maker App Service.

#### <a name="add-ips-to-app-service-allow-list"></a>Lägg till IP-adresser i App Service listan över tillåtna

1. Tillåt endast trafik från Cognitive Services IP-adresser. Dessa ingår redan i Service Tag-numret `CognitiveServicesManagement` . Detta krävs för att skapa API: er (Skapa/uppdatera KB) för att anropa app service och uppdatera Azure Search-tjänsten enligt detta. Läs [Mer information om service märken.](../../../virtual-network/service-tags-overview.md)
2. Se till att du även tillåter andra start punkter som Azure Bot Service, QnA Maker Portal osv. för förutsägelse "GenerateAnswer" API-åtkomst.
3. Följ dessa steg om du vill lägga till IP-adressintervall i en lista över tillåtna:

   1. Hämta [IP-intervall för alla tjänst Taggar](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Välj IP-adresserna för "CognitiveServicesManagement".
   3. Gå till avsnittet nätverk i App Service resursen och klicka på alternativet "konfigurera åtkomst begränsning" för att lägga till IP-adresser i en lista över tillåtna.

Vi har också ett automatiserat skript som gör samma sak för din App Service. Du kan hitta [PowerShell-skriptet för att konfigurera en lista över tillåtna](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) på GitHub. Du måste skriva in prenumerations-ID, resurs grupp och faktiskt App Service namn som skript parametrar. Genom att köra skriptet lägger du automatiskt till IP-adresser i App Service listan över tillåtna adresser.

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
       
    ![inkommande port undantag](../media/inbound-ports.png)

4.  Skapa en QnA Maker kognitiv tjänst instans (Microsoft. CognitiveServices/Accounts) med Azure Resource Manager där QnA Maker-slutpunkten ska ställas in på App Service slut punkten som skapades ovan (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Begränsa åtkomsten till Kognitiv sökning resurs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Kognitiv sökning instans kan isoleras via en privat slut punkt efter att QnA Maker resurserna har skapats. Anslutningar för privata slut punkter kräver ett VNet som Search Service-instansen kan komma åt. 

Om QnA Maker App Service är begränsat med en App Service-miljön använder du samma VNet för att skapa en privat slut punkts anslutning till Kognitiv sökning-instansen. Skapa en ny DNS-post i det virtuella nätverket för att mappa Kognitiv sökning slut punkten till den Kognitiv sökning privata slut punktens IP-adress. 

Om en App Service-miljön inte används för QnAMaker App Service skapar du först en ny VNet-resurs och skapar sedan den privata slut punkts anslutningen till Kognitiv sökning-instansen. I det här fallet måste QnA Maker App Service [integreras med det virtuella](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) nätverket för att ansluta till kognitiv sökning-instansen. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

[Skapa privata slut punkter](../reference-private-endpoint.md) till Azure Search resursen.

---
