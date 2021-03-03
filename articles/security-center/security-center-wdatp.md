---
title: Använda Microsoft Defender för slut punkts licens som ingår i Azure Security Center
description: Lär dig mer om Microsoft Defender för slut punkt och att distribuera den från Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2021
ms.author: memildin
ms.openlocfilehash: b9095d78d902bf5e44bffaba5db19bf2c26e0845
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727013"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Skydda dina slut punkter med Security Center Integrated EDR-lösning: Microsoft Defender för slut punkt

Microsoft Defender för slut punkt är en holistisk, Cloud-levererad slut punkts säkerhets lösning. Huvud funktionerna är:

- Riskfylld sårbarhets hantering och utvärdering 
- Minska attackytan
- Beteende-och moln driven skydd
- Slut punkts identifiering och-svar (EDR)
- Automatisk undersökning och reparation
- Hanterade jakt tjänster

> [!TIP]
> Startades ursprungligen som **Windows Defender ATP**, den här EDR-produkten har bytt namn till 2019 som **Microsoft Defender ATP**.
>
> Vid antändning 2020 lanserade vi [Microsoft Defender XDR Suite](https://www.microsoft.com/security/business/threat-protection) och den här EDR-komponenten har bytt namn till **Microsoft Defender för slut punkt**.


## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:                  | Allmänt tillgänglig (GA)                                                                                                                                                                                                                                                                                      |
| Priset                        | Kräver [Azure Defender för servrar](security-center-pricing.md)                                                                                                                                                                                                                                             |
| Plattformar som stöds:            |  • Azure-datorer som kör Windows<br> • Azure Arc-datorer som kör Windows|
| Versioner av Windows som stöds:  |   • **Allmän tillgänglighet (ga) –** identifiering på Windows Server 2016, 2012 R2 och 2008 R2 SP1<br> • För **hands versions** identifiering på windows Server 2019, [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)och [Windows 10 Enterprise multi-session](../virtual-desktop/windows-10-multisession-faq.md) (tidigare företag för virtuella skriv bord (EVD)<br>Övervakning av Server slut punkt med hjälp av dessa integreringar har inaktiverats för Office 365 GCC-kunder|
| Operativ system som inte stöds:  |  • Windows 10 (annat än EVD eller WVD)<br> • Linux|
| Nödvändiga roller och behörigheter: | Aktivera/inaktivera integrering: **säkerhets administratör** eller **ägare**<br>Så här visar du MDATP-aviseringar i Security Center: **säkerhets läsare**, **läsare**, **resurs grupps deltagare**, **resurs grupp ägare**, **säkerhets administratör**, **prenumerations ägare** eller **prenumerations deltagare**|
| Moln                         | ![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Inga](./media/icons/no-icon.png) Kina gov, andra gov<br>![Inga](./media/icons/no-icon.png) GCC kunder som kör arbets belastningar i globala Azure-moln                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender för slut punkts funktioner i Security Center

Microsoft Defender för slut punkt innehåller:

- **Avancerade sensorer efter intrångs avkänning**. Defender för slut punkts sensorer för Windows-datorer samlar in en omfattande uppsättning beteende signaler.

- **Analys baserad, molnbaserad identifiering efter intrång**. Defender för slut punkten anpassas snabbt för att ändra hot. Den använder avancerade analyser och Big data. Den förstärks av kraften hos Intelligent Security Graph med signaler över Windows, Azure och Office för att identifiera okända hot. Den innehåller aviseringar som kan åtgärdas och gör det möjligt att svara snabbt.

- **Hotinformation**. Defender för slut punkten genererar aviseringar när den identifierar angrepps verktyg, tekniker och procedurer. Den använder data som genererats av Microsoft Threat Hunters och säkerhets team, som tillhandahålls av våra partner.

Genom att integrera Defender för slut punkt med Security Center kan du dra nytta av följande ytterligare funktioner:

- **Automatiserad onboarding**. Security Center aktiverar automatiskt Microsoft Defender för slut punkts sensor för alla Windows-servrar som övervakas av Security Center.

- **En ruta i glaset**. I Security Center-konsolen visas Microsoft Defender för slut punkts varningar. Om du vill undersöka ytterligare använder du Microsoft Defender för slut punktens egna Portal sidor där du kan se ytterligare information som varnings process trädet och incident diagrammet. Du kan också se en detaljerad dator tids linje som visar varje beteende för en historisk period på upp till sex månader.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender för slutpunktens egna Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender för plats för slut punkts klient

När du använder Azure Security Center för att övervaka dina servrar skapas automatiskt en Microsoft Defender för slut punkts klient. Data som samlas in av Defender för slut punkten lagras på den geo-platsen för klient organisationen som identifieras under etableringen. Kund information – i pseudonymized form – kan också lagras i de centrala lagrings-och bearbetnings systemen i USA. 

När du har konfigurerat platsen kan du inte ändra den. Om du har en egen licens för Microsoft Defender för slut punkt och behöver flytta dina data till en annan plats kontaktar du Microsoft Support för att återställa klienten.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Aktivera Microsoft Defender för slut punkts integrering

1. Bekräfta att datorn uppfyller de nödvändiga kraven för Defender för slut punkten:

    - För **alla versioner av Windows**:
        - Konfigurera de nätverks inställningar som beskrivs i [Konfigurera inställningar för enhets proxyserver och Internet anslutning](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
        - Om du distribuerar Defender till slut punkt till en lokal dator ansluter du den till Azure-bågen enligt beskrivningen i [Connect hybrid Machines med Azure Arc-aktiverade servrar](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - För **Windows Server 2019-datorer** kontrollerar du dessutom att de kör en giltig agent och har MicrosoftMonitoringAgent-tillägget

1. Aktivera **Azure Defender för servrar**. Se [prissättning för Azure Security Center](security-center-pricing.md#enable-azure-defender).


1. Om du redan har licensierat och distribuerat Microsoft Defender för slut punkter på dina servrar, tar du bort den med hjälp av proceduren som beskrivs i [avpublicera Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj den prenumeration som du vill ändra.
1. Välj **hot identifiering**.
1. Välj **Tillåt Microsoft Defender för slut punkt för att komma åt mina data** och välj **Spara**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Aktivera integrationen mellan Azure Security Center och Microsofts EDR-lösning, Microsoft Defender för slut punkt":::

    Azure Security Center kommer automatiskt att publicera dina servrar till Microsoft Defender för slut punkt. Onboarding kan ta upp till 24 timmar.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Åtkomst till Microsoft Defender för slut punkts portalen

1. Se till att användar kontot har de behörigheter som krävs. Läs mer i [Tilldela användar åtkomst till Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Kontrol lera om du har en proxy eller brand vägg som blockerar anonym trafik. Defender för slut punkts sensorn ansluter från system kontexten, så anonym trafik måste tillåtas. För att förhindra åtkomst till Defender för slut punkts portalen, följ instruktionerna i [ge åtkomst till tjänst-URL: er i proxyservern](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Öppna [Microsoft Defender Security Center-portalen](https://securitycenter.windows.com/). Läs mer om portalens funktioner och ikoner i [Microsoft Defender Security Center Portal-översikt](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Skicka en test avisering

Så här skapar du en ofarlig Microsoft Defender för slut punkts test avisering:

1. Skapa en mapp ' C:\test-MDATP-test '.
1. Använd fjärr skrivbord för att få åtkomst till datorn.
1. Öppna ett kommandotolksfönster.
1. Kopiera och kör följande kommando i kommando tolken. Kommando tolks fönstret stängs automatiskt.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Ett kommando tolks fönster med kommandot för att generera en test avisering.":::

1. Om kommandot lyckas visas en ny avisering på instrument panelen för Azure Security Center och Microsoft Defender för slut punkts portalen. Den här aviseringen kan ta några minuter innan den visas.
1. Om du vill granska aviseringen i Security Center går du till **säkerhets varningar**  >  ,**misstänkt PowerShell-kommandorad**.
1. I fönstret undersökning väljer du länken för att gå till Microsoft Defender för slut punkts portalen.

    > [!TIP]
    > Aviseringen utlöses med **informationens** allvarlighets grad.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Vanliga frågor och svar om Security Centers integrerade Microsoft Defender för slut punkt

- [Vilka är licens kraven för Microsoft Defender för slut punkten?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Om jag redan har en licens för Microsoft Defender för slut punkten får jag en rabatt för Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hur gör jag för att växlar från ett EDR-verktyg från tredje part?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Vilka är licens kraven för Microsoft Defender för slut punkten?
Defender för slut punkt ingår utan extra kostnad med **Azure Defender för servrar**. Alternativt kan den köpas separat för 50 datorer eller mer.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Om jag redan har en licens för Microsoft Defender för slut punkten får jag en rabatt för Azure Defender?
Om du redan har en licens för Microsoft Defender för slut punkt behöver du inte betala för den delen av din Azure Defender-licens.

Om du vill bekräfta rabatten kontaktar du Security Center support teamet och anger relevant arbetsyte-ID, region och licens information för varje relevant licens.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hur gör jag för att växlar från ett EDR-verktyg från tredje part?
Fullständiga instruktioner för att byta från en slut punkts lösning som inte kommer från Microsoft finns i dokumentationen för Microsoft Defender för slut punkt: [Översikt över migrering](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.