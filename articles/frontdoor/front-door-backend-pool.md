---
title: Server delar och backend-pooler i Azures front dörr | Microsoft Docs
description: Den här artikeln beskriver vilka Server delar och backend-pooler som finns i konfiguration av front dörren.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449301"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Server delar och backend-pooler i Azures front dörr
I den här artikeln beskrivs begrepp för hur du mappar din distribution av webb program med Azures front dörr. Den förklarar också de olika terminologier som används i konfigurationen för den här konfigurationen runt programmets Server delar.

## <a name="backends"></a>Serverdelar
En server del syftar på en distribution av webb program i en region. Frontend-dörren stöder både Azure-och icke-Azure-resurser i backend-poolen. Programmet kan antingen finnas i ditt lokala data Center eller finnas i en annan moln leverantör.

Backend-arbetsdelar syftar på värd namnet eller den offentliga IP-adressen för ditt program som hanterar dina klient förfrågningar. Server delar bör inte förväxlas med databas nivån, lagrings nivån och så vidare. Server delens slut punkter bör visas som den offentliga slut punkten för din program Server del. När du lägger till en server del i en backend-pool för frontend-fack måste du också lägga till följande:

- **Server dels värd typ**. Den typ av resurs som du vill lägga till. Frontend-dörren stöder automatisk identifiering av appens Server delar från App Service, Cloud service eller Storage. Om du vill ha en annan resurs i Azure eller till och med en icke-Azure-Server del väljer du **anpassad värd**.

    >[!IMPORTANT]
    >Under konfigurationen validerar inte API: erna om Server delen inte är tillgänglig från Front dörr miljöer. Kontrol lera att front dörren kan komma åt din server del.

- **Namn på prenumeration och backend-värd**. Om du inte har valt en **anpassad värd** för Server dels värd typ väljer du din server del genom att välja lämplig prenumeration och motsvarande Server dels värd namn i användar gränssnittet.

- **Värd rubrik för Server** del. Värd huvud värde som skickas till Server delen för varje begäran. Mer information finns i [värd rubriken för Server delen](#hostheader).

- **Prioritet**. Tilldela prioriteter till olika Server delar när du vill använda en primär tjänst Server del för all trafik. Du kan också ange säkerhets kopior om den primära säkerhets kopian eller säkerhets kopieringen inte är tillgänglig. Mer information finns i [prioritet](front-door-routing-methods.md#priority).

- **Vikt**. Tilldela vikter till dina olika Server delar för att distribuera trafik över en uppsättning av Server delar, antingen jämnt eller enligt viktade koefficienter. Mer information finns i [vikter](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Värd rubrik för Server del

Begär Anden som vidarebefordras av front dörren till en server del är ett värd huvud fält som server delen använder för att hämta mål resursen. Värdet för det här fältet kommer normalt från Server dels-URI: n som har värd rubriken och-porten.

Till exempel har en begäran som gjorts för `www.contoso.com` ha värd rubriken www.contoso.com. Om du använder Azure Portal för att konfigurera server delen är standardvärdet för det här fältet värd namnet för Server delen. Om Server delen är contoso-westus.azurewebsites.net, i Azure Portal, kommer det automatiskt ifyllda värdet för värd rubriken för Server delen att vara contoso-westus.azurewebsites.net. Men om du använder Azure Resource Manager mallar eller någon annan metod utan att ange det här fältet, skickar front dörren det inkommande värd namnet som värde för värd huvudet. Om begäran har gjorts för www \. -contoso.com och din server del är contoso-westus.azurewebsites.net som har ett tomt huvud fält, kommer klient dörren att ange värd rubriken som www- \. contoso.com.

De flesta Server Server delar (Azure Web Apps, Blob Storage och Cloud Services) kräver att värd huvudet matchar Server delens domän. Dock använder klient dels värden som dirigerar till Server delen ett annat värdnamn, till exempel www.contoso.net.

Om Server delen kräver värd rubriken för att matcha Server delens värdnamn, måste du kontrol lera att Server delens värd huvud innehåller Server delens värd namn.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurera Server delens värd rubrik för Server delen

Så här konfigurerar du fältet **värd huvud** för Server delen i avsnittet Server dels pool:

1. Öppna din frontend-resurs och välj den backend-pool som ska konfigureras av Server delen.

2. Lägg till en server del om du inte har gjort det eller redigera en befintlig.

3. Ange ett eget värde för värd huvud fältet för Server delen eller lämna det tomt. Värd namnet för den inkommande begäran kommer att användas som värd huvud värde.

## <a name="backend-pools"></a>Backend-pooler
En backend-pool i front dörren syftar på den uppsättning Server delar som tar emot liknande trafik för appen. Det är med andra ord en logisk gruppering av dina App-instanser över hela världen som får samma trafik och svarar med förväntat beteende. Dessa Server delar distribueras i olika regioner eller inom samma region. Alla Server delar kan vara i aktivt/aktivt distributions läge eller vad som definieras som aktiv/passiv konfiguration.

En backend-pool definierar hur de olika Server delarna ska utvärderas via hälso avsökningar. Den definierar också hur belastnings utjämning sker mellan dem.

### <a name="health-probes"></a>Hälsotillståndsavsökningar
Front dörren skickar regelbundna HTTP/HTTPS-avsöknings begär anden till var och en av dina konfigurerade Server delar. Avsöknings begär Anden fastställer närhet och hälsan för varje server del för att belastningsutjämna dina slut användar förfrågningar. Hälso avsöknings inställningar för en backend-pool definierar hur vi avsöker hälso tillståndet för app-arbetsdelar. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

- **Sökväg**: den URL som används för avsöknings begär Anden för alla Server delar i backend-poolen. Om t. ex. en av dina Server delar är contoso-westus.azurewebsites.net och sökvägen är inställd på/PROBE/test.aspx, skickas begär Anden om hälso avsökningar till http//contoso-westus.azurewebsites.net/probe/test.aspx i front dörrs miljöer \: .

- **Protokoll**: definierar om hälso avsöknings förfrågningar ska skickas från Front dörren till dina Server delar med http-eller HTTPS-protokoll.

- **Metod**: den http-metod som ska användas för att skicka hälso avsökningar. Alternativen är GET eller HEAD (standard).
    > [!NOTE]
    > För att minska belastningen och kostnaden på dina Server delar rekommenderar front dörren att använda HEAD-begäranden för hälso avsökningar.

- **Intervall (sekunder)**: definierar frekvensen för hälso avsökningar i dina Server delar, eller i de intervall där varje front dörr miljö skickar en avsökning.

    >[!NOTE]
    >För snabbare redundans anger du intervallet till ett lägre värde. Ju lägre värde, desto högre hälso avsöknings volym som dina Server delar får. Exempel: om intervallet är inställt på 30 sekunder, med 100 front-dörr pop globalt, kommer varje server del att ta emot cirka 200 avsöknings begär Anden per minut.

Mer information finns i [hälso avsökningar](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Inställningar för belastnings utjämning
Inställningar för belastnings utjämning för backend-poolen definierar hur vi utvärderar hälso avsökningar. De här inställningarna avgör om Server delen är felfri eller ohälsosam. De kan också kontrol lera belastnings Utjämnings trafik mellan olika Server delar i backend-poolen. Följande inställningar är tillgängliga för belastnings Utjämnings konfiguration:

- **Exempel storlek**. Identifierar hur många prover av hälso avsökningar vi behöver ta hänsyn till vid utvärdering av Server dels hälsa.

- **Samplings storlek har slutförts**. Definierar den exempel storlek som tidigare nämnts, antalet lyckade exempel som krävs för att anropa Server delens hälsosamhet. Anta till exempel att ett hälso avsöknings intervall på en front dörr är 30 sekunder, exempel storleken är 5 och att prov storleken är 3. Varje gången vi utvärderar hälso avsökningar för din server del tittar vi på de senaste fem exemplen över 150 sekunder (5 × 30). Det krävs minst tre lyckade avsökningar för att deklarera Server delen som felfri.

- **Fördröjnings känslighet (ytterligare svars tid)**. Anger om du vill att frontend-dörren ska skicka begäran till Server delar inom ett känslighets intervall för svars tider eller vidarebefordra begäran till närmaste Server del.

Mer information finns i [metod för minsta latens baserad routning](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för en front dörr](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)
