---
title: Övervakning av fil i Azure Security Center
description: Lär dig hur du jämför bas linjer med övervakning av fil integritet i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634712"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Jämföra baslinjer med hjälp av övervakning av filintegritet (FIM)

File Integrity Monitoring (FIM) informerar dig när ändringar sker i känsliga områden i resurserna, så att du kan undersöka och åtgärda otillåten aktivitet. FIM övervakar Windows-filer, Windows-register och Linux-filer.

I det här avsnittet beskrivs hur du aktiverar FIM på filer och register. Mer information om FIM finns i avsnittet [om fil integritets övervakning i Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Varför ska jag använda FIM?

Operativ system, program och associerade konfigurationer styr dina resursers beteende och säkerhets tillstånd. Därför riktar angriparen filerna som styr resurserna, för att kunna ta en resurss operativ system och/eller köra aktiviteter utan att identifieras.

I själva verket kräver många reglerande standarder som PCI-DSS & ISO 17799 implementering av FIM-kontroller.  

## <a name="enable-built-in-recursive-registry-checks"></a>Aktivera inbyggda rekursiva register kontroller

Standardvärdena för FIM-Hive är ett bekvämt sätt att övervaka rekursiva ändringar inom vanliga säkerhets områden.  En angripare kan till exempel konfigurera ett skript som ska köras i LOCAL_SYSTEM-kontext genom att konfigurera en körning vid start eller avstängning.  Aktivera den inbyggda kontrollen för att övervaka ändringar av den här typen.  

![Register](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekursiva kontroller gäller endast för rekommenderade säkerhets strukturer och inte anpassade register Sök vägar.  

## <a name="add-a-custom-registry-check"></a>Lägg till en anpassad register kontroll

FIM-slutpunkter börjar genom att identifiera egenskaper för ett tillförlitligt tillstånd för operativ systemet och program som stöds.  I det här exemplet kommer vi att fokusera på konfigurationerna för lösen ords principer för Windows Server 2008 och högre.


|Principnamn                 | Registerinställning|
|---------------------------------------|-------------|
|Domänkontrollant: Tillåt inte ändringar av lösenord för datorkonton| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Domänmedlem: Kryptera eller signera data för säker kanal digitalt (alltid)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Domänmedlem: Kryptera data för säker kanal digitalt (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Domänmedlem: Signera säkra kanaldata digitalt (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Domänmedlem: Inaktivera ändringar av lösenord för datorkonton|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Domänmedlem: Högsta ålder för lösenord för datorkontot|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Domänmedlem: Kräv stark sessionsnyckel (Windows 2000 eller senare)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Nätverks säkerhet: Begränsa NTLM: NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Nätverkssäkerhet: Begränsa NTLM: Lägg till serverundantag i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Nätverks säkerhet: Begränsa NTLM: Granska NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Mer information om register inställningar som stöds av olika operativ system versioner finns i [kalkyl bladet grupprincip inställningar referens](https://www.microsoft.com/download/confirmation.aspx?id=25250).

Konfigurera FIM för att övervaka register bas linjer:

1. I fönstret **Lägg till Windows-register för ändringsspårning** i text rutan **register nyckel för Windows** anger du följande register nyckel:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Aktivera FIM i ett register":::

## <a name="track-changes-to-windows-files"></a>Spåra ändringar i Windows-filer

1. I text rutan **Ange sökväg** i fönstret **Lägg till Windows-fil för ändringsspårning** anger du den mapp som innehåller de filer som du vill spåra. I exemplet i följande figur finns **contoso-webbappen** i D:\ enhet i mappstrukturen för **ContosWebApp** .  
1. Skapa en anpassad Windows-filpost genom att ange ett namn på inställnings klassen, aktivera rekursion och ange den översta mappen med ett jokertecken (*).

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Aktivera FIM på en fil":::

## <a name="retrieve-change-data"></a>Hämta ändrings data

Data för fil integritets övervakning finns i tabell uppsättningen för Azure Log Analytics/ConfigurationChange.  

 1. Ange ett tidsintervall för att hämta en sammanfattning av ändringar per resurs.
I följande exempel hämtar vi alla ändringar under de senaste fjorton dagarna i kategorier av register och filer:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Så här visar du information om registrets ändringar:

    1. Ta bort **filer** från **WHERE** -satsen 
    1. Ta bort sammanfattnings raden och ersätt den med en order-sats:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Rapporter kan exporteras till CSV för arkivering och/eller kanal till en Power BI rapport.  

![FIM-data](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)