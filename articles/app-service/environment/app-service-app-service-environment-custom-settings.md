---
title: Konfigurera anpassade inställningar
description: Konfigurera inställningar som gäller för hela Azure App Services miljön. Lär dig hur du gör det med Azure Resource Manager mallar.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226396"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Anpassade konfigurationsinställningar för App Service-miljöer
## <a name="overview"></a>Översikt
Eftersom App Service-miljöer (ASE) är isolerade i en enda kund finns det vissa konfigurationsinställningar som kan användas exklusivt för App Service-miljöer. I den här artikeln beskrivs de olika anpassningar som är tillgängliga för App Service-miljöer.

Om du inte har en App Service-miljö kan du läsa [Skapa en App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Du kan lagra App Service-miljöanpassningar med hjälp av en matris i det nya attributet **clusterSettings**. Det här attributet finns i ordlistan ”Egenskaper” för *hostingEnvironments* Azure Resource Manager-entiteten.

Följande förkortade Resource Manager-mallfragment visar attributet **clusterSettings**:

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

Attributet **clusterSettings** kan ingå i en Resource Manager-mall för att uppdatera App Service-miljön.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Använda Azure Resource Explorer för att uppdatera en App Service-miljö
Du kan också uppdatera App Service-miljön med hjälp av [Azure Resource Explorer](https://resources.azure.com).  

1. I Resursläsaren går du till noden för App Service-miljön (**Subscriptions**  >  **resourceGroups**-  >  **providers**  >  **Microsoft. Web**  >  **hostingEnvironments**). Klicka sedan på den specifika App Service-miljö du vill uppdatera.
2. I den högra rutan klickar du på **Läs/Skriv** i det övre verktygsfältet för att tillåta interaktiv redigering i resursutofrskaren.  
3. Klicka på den blå **redigeringsknappen** att Resource Manager-mallen kan redigeras.
4. Rulla längst ned i den högra rutan. Attributet **clusterSettings** är allra längst ned på sidan, där du kan ange eller uppdatera dess värde.
5. Ange (eller kopiera och klistra in) matrisen med konfigurationsvärden som du vill ha i attributet **clusterSettings**.  
6. Klicka på den gröna knappen **PUT** (Placera) som finns högst upp i det högra fönstret för att genomföra ändringen i App Service-miljön.

Du skickar hur som helst in ändringen. Det tar ungefär 30 minuter gånger antalet klientdelar i App Service-miljön innan ändringen träder i kraft.
Om en App Service-miljö exempelvis har fyra klientdelar tar det ungefär två timmar för konfigurationsuppdateringen att slutföras. Medan konfigurationsändringen distribueras kan inga andra skalningsåtgärder eller ändra konfigurationsåtgärder äga rum i App Service-miljön.

## <a name="enable-internal-encryption"></a>Aktivera intern kryptering

App Service-miljön fungerar som ett svart Box-system där du inte kan se interna komponenter eller kommunikationen i systemet. Om du vill aktivera högre data flöde är kryptering inte aktiverat som standard mellan interna komponenter. Systemet är säkert eftersom trafiken inte är tillgänglig för övervakning eller åtkomst. Om du har ett krav på efterlevnad, men som kräver fullständig kryptering av data Sök vägen från slut punkt till slut punkt, finns det ett sätt att aktivera kryptering av den fullständiga data Sök vägen med en clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Genom att ange InternalEncryption till True krypteras intern nätverks trafik i din ASE mellan klient delar och arbetare, krypterar växlings filen och krypterar även arbets diskarna. När InternalEncryption-clusterSetting har Aktiver ATS kan det påverka systemets prestanda. När du gör ändringen för att aktivera InternalEncryption, kommer ASE att vara i ett instabilt tillstånd tills ändringen har spridits helt. Det kan ta några timmar att slutföra spridningen av ändringen, beroende på hur många instanser du har i din ASE. Vi rekommenderar starkt att du inte aktiverar InternalEncryption på en ASE medan den används. Om du behöver aktivera InternalEncryption på en aktivt Använd ASE, rekommenderar vi starkt att du avinstallerar trafik till en säkerhets kopierings miljö tills åtgärden har slutförts. 


## <a name="disable-tls-10-and-tls-11"></a>Inaktivera TLS 1.0 och TLS 1.1

Om du vill hantera TLS-inställningar separat för varje app kan du använda de riktlinjer som tillhandahålls med dokumentationen för att [framtvinga TLS-inställningar](../configure-ssl-bindings.md#enforce-tls-versions). 

Om du vill inaktivera all inkommande TLS 1.0- och TLS 1.1-trafik för alla appar i en ASE kan du ange följande **clusterSettings**-post:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

Namnet på inställningen har värdet 1.0, men när den konfigureras inaktiverar den både TLS 1.0 och TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Ändra ordning på TLS-chiffersvit
ASE stöder ändring av cipher-sviten från standardvärdet. Standard uppsättningen med chiffer är samma uppsättning som används i tjänsten för flera innehavare. Att ändra chiffersviter påverkar en hel App Service distribution som gör detta endast möjligt i ASE för en klient. Det krävs två chiffersviter för en ASE-serie. TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 och TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Om du vill använda ASE med den starkaste och mest minimala uppsättningen chiffersviter, använder du bara de två nödvändiga chiffer. Om du vill konfigurera dina ASE så att de bara använder de chiffer som krävs ändrar du **clusterSettings** enligt nedan. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Om felaktiga värden har angetts för chiffersviten som SChannel inte förstår kan all TLS-kommunikation till servern sluta fungera. I sådana fall måste du ta bort posten *FrontEndSSLCipherSuiteOrder* post från **clusterSettings** och skicka den uppdaterade Resource Manager-mallen för att återgå till standardchiffersvitinställningarna.  Använd den här funktionen med försiktighet.

## <a name="get-started"></a>Kom igång
På mallwebbplatsen för Azure-snabbstarten för Resource Manager finns en mall med basdefinitionen för att [skapa en App Service-miljö](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
