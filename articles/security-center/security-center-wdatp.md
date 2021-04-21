---
title: Använda Microsoft Defender for Endpoint-licensen som ingår i Azure Security Center
description: Lär dig mer om Microsoft Defender för slutpunkt och hur du distribuerar det från Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829898"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Skydda dina slutpunkter med Security Center integrerade EDR-lösningen: Microsoft Defender för slutpunkt

Microsoft Defender för slutpunkt är en holistisk, moln levererad slutpunktssäkerhetslösning. De viktigaste funktionerna är:

- Riskbaserad sårbarhetshantering och utvärdering 
- Minska attackytan
- Beteendebaserat och molnbaserat skydd
- Slutpunktsidentifiering och svar (EDR)
- Automatisk undersökning och reparation
- Hanterade jakttjänster

> [!TIP]
> Den här **EDR-produkten**(Endpoint Detection and Response) startades ursprungligen som Windows Defender ATP och bytte namn 2019 till **Microsoft Defender ATP.**
>
> På Ignite 2020 lanserade vi [Microsoft Defender XDR-sviten](https://www.microsoft.com/security/business/threat-protection) och den här EDR-komponenten har bytt namn **till Microsoft Defender för slutpunkt.**


## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utgivningstillstånd:                  | Allmänt tillgänglig (GA)                                                                                                                                                                                                                                                                                      |
| Prissättning:                        | Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Plattformar som stöds:            |  • Azure-datorer som kör Windows<br> • Azure Arc datorer som kör Windows|
| Versioner av Windows som stöds för identifiering:  |  • Windows Server 2019, 2016, 2012 R2 och 2008 R2 SP1<br> • [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise flera sessioner](../virtual-desktop/windows-10-multisession-faq.yml) (tidigare Enterprise for Virtual Desktops (EVD)|
| Operativsystem som inte stöds:  |  • Windows 10 (förutom EVD eller WVD)<br> • Linux|
| Nödvändiga roller och behörigheter: | Så här aktiverar/inaktiverar du integreringen: **Säkerhetsadministratör** eller **ägare**<br>Så här visar du MDATP-aviseringar **Security Center: Säkerhetsläsare,** **Läsare,** Resursgruppsdeltagare, Resursgruppsägare, Säkerhetsadministratör, **Prenumerationsägare** eller **Prenumerationsdeltagare** |
| Moln:                         | ![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender för slutpunktsfunktioner i Security Center

Microsoft Defender för slutpunkt tillhandahåller:

- **Avancerade sensorer för identifiering efter intrång.** Defender för slutpunktssensorer för Windows-datorer samlar in en mängd olika beteendesignaler.

- **Analysbaserad, molnbaserad identifiering** efter intrång. Defender for Endpoint anpassas snabbt efter föränderliga hot. Den använder avancerad analys och stordata. Den förstärks av kraften hos de virtuella Intelligent Security Graph signaler i Windows, Azure och Office för att identifiera okända hot. Den innehåller åtgärdsbara aviseringar och gör att du kan svara snabbt.

- **Hotinformation**. Defender for Endpoint genererar aviseringar när den identifierar angriparens verktyg, tekniker och procedurer. Den använder data som genereras av Microsofts hotteam och säkerhetsteam, förstärkta av information från partner.

Genom att integrera Defender för slutpunkt Security Center kan du dra nytta av följande ytterligare funktioner:

- **Automatiserad registrering.** Security Center automatiskt microsoft Defender for Endpoint-sensorn för alla Windows-servrar som övervakas av Security Center.

- **En fönsterruta i ett fönster.** Konsolen Security Center visar Microsoft Defender för slutpunktsaviseringar. Om du vill undersöka vidare kan du använda Microsoft Defender för slutpunktens egna portalsidor där du ser ytterligare information, till exempel aviseringsprocessträdet och incidentdiagrammet. Du kan också se en detaljerad tidslinje för datorn som visar varje beteende för en historisk period på upp till sex månader.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender för slutpunktens egna Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Vilka är kraven för klientorganisationen Microsoft Defender för slutpunkt?

När du använder Azure Security Center för att övervaka dina servrar skapas automatiskt en Microsoft Defender for Endpoint-klientorganisation. 

- **Plats:** Data som samlas in av Defender för slutpunkt lagras på den geografiska platsen för klientorganisationen som identifierades under etableringen. Kunddata – i pseudonymiserad form – kan också lagras i de centrala lagrings- och bearbetningssystemen i USA. När du har konfigurerat platsen kan du inte ändra den. Om du har en egen licens för Microsoft Defender för slutpunkt och behöver flytta dina data till en annan plats kontaktar du Microsoft Support för att återställa klientorganisationen.
- **Flytta prenumerationer:** Om du har flyttat din Azure-prenumeration mellan Azure-klienter krävs vissa manuella förberedande steg innan Security Center distribuera Defender för slutpunkt. Kontakta [Microsofts support för fullständig information.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Aktivera integrering av Microsoft Defender för slutpunkter

### <a name="prerequisites"></a>Förutsättningar

Bekräfta att datorn uppfyller de nödvändiga kraven för Defender för slutpunkt:

1. Kontrollera att datorn är ansluten till Azure efter behov:

    - För **Windows-servrar** konfigurerar du nätverksinställningarna som beskrivs i [Konfigurera inställningar för enhetsproxy och Internetanslutning](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
    - För **lokala datorer ansluter du** den till en Azure Arc förklaras i Ansluta [hybriddatorer med Azure Arc aktiverade servrar](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - För **Windows Server 2019-** [och Windows Virtual Desktop-datorer (WVD)](../virtual-desktop/overview.md) bekräftar du att datorerna kör Log Analytics-agenten och har tillägget MicrosoftMonitoringAgent.
    
1. Aktivera **Azure Defender för servrar**. Se [Snabbstart: Aktivera Azure Defender](enable-azure-defender.md).
1. Om du redan har licensierat och distribuerat Microsoft Defender for Endpoints på dina servrar tar du bort den med hjälp av proceduren som beskrivs [i Avkorta Windows-servrar.](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)
1. Om du har flyttat din prenumeration mellan Azure-klienter krävs även vissa manuella förberedande steg. Kontakta [Microsofts support om du vill ha fullständig information.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Aktivera integreringen
1. På Security Center väljer du Prisinställningar **& och** väljer den prenumeration som du vill ändra.
1. Välj **Hotidentifiering.**
1. Välj **Allow Microsoft Defender for Endpoint to access my data (Tillåt att Microsoft Defender för slutpunkt** får åtkomst till mina data) och välj **Spara**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Aktivera integrering mellan Azure Security Center och Microsofts EDR-lösning, Microsoft Defender för slutpunkt":::

    Azure Security Center automatiskt dina servrar till Microsoft Defender för slutpunkt. Onboarding kan ta upp till 24 timmar.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Få åtkomst till Microsoft Defender för slutpunktsportalen

1. Kontrollera att användarkontot har nödvändiga behörigheter. Läs mer i [Tilldela användaråtkomst till Microsoft Defender Säkerhetscenter](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Kontrollera om du har en proxy eller brandvägg som blockerar anonym trafik. Defender for Endpoint-sensorn ansluter från systemkontexten, så anonym trafik måste tillåtas. Följ anvisningarna i Aktivera åtkomst till tjänst-URL:er på proxyservern för att säkerställa obegränsad åtkomst till Defender [for Endpoint-portalen.](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

1. Öppna [Microsoft Defender Säkerhetscenter portalen.](https://securitycenter.windows.com/) Läs mer om portalens funktioner och ikoner i översikten [Microsoft Defender Säkerhetscenter portalen.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Skicka en testavisering

Så här genererar du en ofarlig testavisering för Microsoft Defender för slutpunkt:

1. Skapa mappen "C:\test-MDATP-test".
1. Använd Fjärrskrivbord för att komma åt datorn.
1. Öppna ett kommandotolksfönster.
1. Kopiera och kör följande kommando i kommandotolken. Kommandotolken stängs automatiskt.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Ett kommandotolkfönster med kommandot för att generera en testavisering.":::

1. Om kommandot lyckas visas en ny avisering på instrumentpanelen Azure Security Center microsoft Defender för slutpunkt. Den här aviseringen kan ta några minuter att visas.
1. Om du vill granska aviseringen i Security Center du till **Säkerhetsaviseringar**  >  **Misstänkt PowerShell-kommandorad.**
1. I undersökningsfönstret väljer du länken för att gå till Microsoft Defender för slutpunktsportalen.

    > [!TIP]
    > Aviseringen utlöses med **allvarlighetsgrad** för information.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Vanliga frågor Security Center om microsoft Defender för slutpunkter

- [Vilka är licenskraven för Microsoft Defender för slutpunkt?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Om jag redan har en licens för Microsoft Defender för slutpunkt kan jag få rabatt för Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hur gör jag för att byta från ett EDR-verktyg från tredje part?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Vilka är licensieringskraven för Microsoft Defender för slutpunkt?
Defender for Endpoint ingår utan extra kostnad med Azure Defender **för servrar**. Du kan också köpa den separat för 50 datorer eller mer.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Om jag redan har en licens för Microsoft Defender för slutpunkt kan jag få rabatt för Azure Defender?
Om du redan har en licens för Microsoft Defender for Endpoint behöver du inte betala för den delen av din Azure Defender licens.

Om du vill bekräfta rabatten kontaktar Security Center supportteamet och anger relevant arbetsyte-ID, region och licensinformation för varje relevant licens.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hur gör jag för att byta från ett EDR-verktyg från tredje part?
Fullständiga anvisningar för att växla från en slutpunktslösning som inte kommer från Microsoft finns i dokumentationen för Microsoft Defender for Endpoint: [Migreringsöversikt](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Hantera säkerhetsrekommendationer i Azure Security Center:](security-center-recommendations.md)Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.