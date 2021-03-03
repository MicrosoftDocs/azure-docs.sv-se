---
title: Avancerad attack identifiering i Multistage i Azure Sentinel
description: Använd fusions teknik i Azure Sentinel för att minska varnings utmattningen och skapa åtgärds bara incidenter som baseras på avancerad attack identifiering i multiskede.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724293"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Avancerad attack identifiering i Multistage i Azure Sentinel

> [!IMPORTANT]
> Vissa fusions identifieringar (se de som anges nedan) finns för närvarande i för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Genom att använda fusions teknik som baseras på maskin inlärning kan Azure Sentinel automatiskt identifiera attacker med flera steg genom att identifiera kombinationer av avvikande beteenden och misstänkta aktiviteter som observeras i olika steg i Kill-kedjan. På grund av dessa identifieringar genererar Azure Sentinel incidenter som annars skulle vara svåra att fånga. Dessa incidenter består av två eller flera aviseringar eller aktiviteter. Som design är dessa incidenter låg volym, hög åter givning och hög allvarlighets grad.

Den här identifierings tekniken är anpassad för din miljö och minskar bara falsk positiv taxa men kan också identifiera attacker med begränsad eller saknad information.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguration för avancerad attackidentifiering i flera faser

Den här identifieringen är aktive rad som standard i Azure Sentinel. Om du vill kontrol lera statusen eller inaktivera den i händelse av att du använder en annan lösning för att skapa incidenter baserade på flera aviseringar kan du använda följande instruktioner:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).

1. Navigera till   >  **konfigurations**  >  **analys** för Azure Sentinel

1. Välj **aktiva regler** och leta sedan upp **Avancerad sårbarhets identifiering i Multistage** i kolumnen **namn** genom att filtrera listan för typen av **fusions** regel. Kontrol lera kolumnen **status** för att bekräfta om identifieringen är aktive rad eller inaktive rad.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Om du vill ändra status väljer du den här posten och på bladet **Avancerad sårbarhets identifiering i Multistage** väljer du **Redigera**.

1. På bladet **Guide för skapande av regel** väljs ändringen av status automatiskt åt dig, så välj **Nästa: granska** och **Spara**. 

 Eftersom **fusions** regel typen bara innehåller en regel som inte kan ändras, gäller inte regelmallar för den här regel typen.

> [!NOTE]
> Azure Sentinel använder för närvarande 30 dagars historiska data för att träna Machine Learning-systemen. Dessa data krypteras alltid med Microsofts nycklar när de passerar genom Machine Learning-pipeline. Inlärnings informationen är dock inte krypterad med [Kundhanterade nycklar (CMK)](customer-managed-keys.md) om du har aktiverat CMK i Azure Sentinel-arbetsytan. Om du inte vill använda fusion går du till **Azure Sentinel** \> **Configuration** \> **Analytics \> Active Rules \> Advanced sårbarhets identifiering i Multistage** och i kolumnen **status** väljer du **Inaktivera.**

## <a name="attack-detection-scenarios"></a>Scenarier för attack identifiering

I följande avsnitt visas olika typer av korrelations scenarier, grupperade efter hot klassificering, som Azure Sentinel letar efter med fusions teknik.

Som nämnts ovan, eftersom fusionen korrelerar flera säkerhets aviseringar från olika produkter för att identifiera avancerade attacker med flera steg, presenteras lyckade fusions **incidenter som fusions incidenter** på sidan Azure Sentinel- **incidenter** och inte som **aviseringar** i tabellen **säkerhets aviseringar** i **loggar**.

Alla data källor som anges måste matas in med de associerade Azure Sentinel-dataanslutningarna för att kunna aktivera dessa scenarier med fusions drivna angrepp.

> [!NOTE]
> Några av dessa scenarier är i för **hands version**. De kommer att anges.

## <a name="compute-resource-abuse"></a>Missbruk av beräknings resurser

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Flera aktiviteter för att skapa virtuella datorer efter misstänkt Azure Active Directory inloggning
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, påverkan 

**Mitre att&CK tekniker:** Giltigt konto (T1078), resurs kapning (T1496)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal virtuella datorer skapades i en enda session efter en misstänkt inloggning till ett Azure AD-konto. Den här typen av avisering indikerar, med hög exakthet, att det konto som anges i beskrivningen av fusions incidenten har komprometterats och använts för att skapa nya virtuella datorer för obehöriga användning, till exempel att köra åtgärder för kryptografisk utvinning. Permutationerna för misstänkta Azure AD-inloggningar med aviseringar om flera aktiviteter för virtuella datorer är:

- **Omöjlig resa till en ovanlig-plats som leder till flera aktiviteter för att skapa virtuella datorer**

- **Inloggnings händelse från en okänd plats som leder till flera aktiviteter för att skapa virtuella datorer**

- **Inloggnings händelse från en infekterad enhet som leder till flera aktiviteter för att skapa virtuella datorer**

- **Inloggnings händelse från en anonym IP-adress som leder till flera aktiviteter för att skapa virtuella datorer**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till flera aktiviteter för att skapa virtuella datorer**

## <a name="credential-harvesting-new-threat-classification"></a>Fångst av autentiseringsuppgifter (ny hot klassificering)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Körnings verktyg för stöld av skadlig autentiseringsuppgift efter misstänkt inloggning

**Mitre att&CK taktiker:** Initial åtkomst, åtkomst till autentiseringsuppgifter

**Mitre att&CK tekniker:** Giltigt konto (T1078), dumpning av operativ systemets autentiseringsuppgift (T1003)

**Data anslutnings källor:** Azure Active Directory Identity Protection, Microsoft Defender för slut punkt

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett känt verktyg för att stjäla autentiseringsuppgifter kördes efter en misstänkt Azure AD-inloggning. Detta ger en hög exakthet som anger att det användar konto som anges i aviserings beskrivningen har komprometterats och kan ha använt ett verktyg som **Mimikatz** för att hämta autentiseringsuppgifter, till exempel nycklar, lösen ord för klartext och/eller lösen ords-hashar från systemet. De skördade autentiseringsuppgifterna kan göra det möjligt för en angripare att komma åt känsliga data, eskalera privilegier och/eller flytta över nätverket senare. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen stöldskydds verktyg för illasinnade autentiseringsuppgifter är:

- **Omöjligt att resa till ovanlig-platser som leder till att körning av skadliga autentiseringsuppgifter körs**

- **Inloggnings händelse från en okänd plats som leder till att stöldskydds verktyget för illasinnade autentiseringsuppgifter körs**

- **Inloggnings händelse från en infekterad enhet som leder till att stöldskydds verktyget för skadliga autentiseringsuppgifter körs**

- **Inloggnings händelse från en anonym IP-adress som leder till att stöldskydds verktyget för illasinnade autentiseringsuppgifter körs**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till att stöldskydds verktyget för skadlig autentiseringsuppgift körs**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Stöld aktivitet för misstänkt autentiseringsuppgift efter misstänkt inloggning

**Mitre att&CK taktiker:** Initial åtkomst, åtkomst till autentiseringsuppgifter

**Mitre att&CK tekniker:** Giltigt konto (T1078), autentiseringsuppgifter från lösen ord arkiv (T1555), dumpning av OS-autentisering (T1003)

**Data anslutnings källor:** Azure Active Directory Identity Protection, Microsoft Defender för slut punkt

**Beskrivning:** Fusions incidenter av den här typen anger att aktiviteten som är kopplad till mönster för stöld av autentiseringsuppgifter uppstår efter en misstänkt Azure AD-inloggning. Detta ger en hög exakthet som anger att det användar konto som anges i aviserings beskrivningen har komprometterats och använts för att stjäla autentiseringsuppgifter, t. ex. nycklar, lösen ord för klartext, lösen ords-hashar och så vidare. De stulna autentiseringsuppgifterna kan göra det möjligt för en angripare att komma åt känsliga data, eskalera privilegier och/eller flytta över nätverket senare. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen om stöld aktivitet för autentiseringsuppgifter är:

- **Omöjlig resa till ovanlig-platser som leder till misstänkt aktivitet för stöld av autentiseringsuppgifter**

- **Inloggnings händelse från en okänd plats som leder till misstänkt aktivitet för stöld av autentiseringsuppgifter**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt aktivitet för stöld av autentiseringsuppgifter**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt aktivitet för stöld av autentiseringsuppgifter**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt aktivitet för stöld av autentiseringsuppgifter**

## <a name="crypto-mining-new-threat-classification"></a>Krypto-utvinning (ny hot klassificering)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Kryptografi-utvinnings aktivitet efter misstänkt inloggning

**Mitre att&CK taktiker:** Initial åtkomst, åtkomst till autentiseringsuppgifter

**Mitre att&CK tekniker:** Giltigt konto (T1078), resurs kapning (T1496)

**Data anslutnings källor:** Azure Active Directory Identity Protection, Azure Defender (Azure Security Center)

**Beskrivning:** Fusions incidenter av den här typen indikerar krypterings utvinnings aktivitet som är associerad med en misstänkt inloggning till ett Azure AD-konto. Detta ger en hög exakthet som anger att det användar konto som anges i aviserings beskrivningen har komprometterats och använts för att kapning av resurser i din miljö till mina kryptografi-valuta. Detta kan strypa resurser för dator kraft och/eller resultera i betydligt högre än förväntad moln användnings räkningar. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen om kryptografiska aktiviteter är:  

- **Omöjlig resa till ovanlig platser som leder till kryptografi-utvinnings aktivitet**

- **Inloggnings händelse från en okänd plats som leder till kryptografi-utvinnings aktivitet**

- **Inloggnings händelse från en infekterad enhet som leder till kryptografi-utvinnings aktivitet**

- **Inloggnings händelse från en anonym IP-adress som leder till kryptografi-utvinnings aktivitet**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till kryptografi-utvinnings aktivitet**

## <a name="data-exfiltration"></a>Dataexfiltrering

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Office 365-brevlåda exfiltrering efter en misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, exfiltrering, samling

**Mitre att&CK tekniker:** Giltigt konto (T1078), e-postsamling (T1114), automatiserad exfiltrering (T1020)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att en misstänkt vidarebefordrings regel för Inkorgen angavs i en användares inkorg efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att användarens konto (som anges i fusions incident beskrivningen) har komprometterats och att det används för att stjäla data från din organisations nätverk genom att aktivera en regel för vidarebefordran av post lådor utan den faktiska användarens vetskap. Permutationerna för misstänkta aviseringar om Azure AD-inloggningar med Office 365-brevlådan exfiltrering-avisering är:

- **Omöjlig resa till en ovanlig-plats som leder till Office 365 Mailbox exfiltrering**

- **Inloggnings händelse från en okänd plats som leder till Office 365 Mailbox exfiltrering**

- **Inloggnings händelse från en infekterad enhet som leder till Office 365 Mailbox exfiltrering**

- **Inloggnings händelse från en anonym IP-adress som leder till Office 365 Mailbox exfiltrering**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Office 365 Mailbox exfiltrering**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Hämtning av Mass fil efter misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, exfiltrering

**Mitre att&CK tekniker:** Giltigt konto (T1078)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal filer har hämtats av en användare efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att stjäla data från din organisations nätverk. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen Mass hämtnings fil är:  

- **Omöjlig resa till en ovanlig plats som leder till Mass hämtning av filer**

- **Inloggnings händelse från en okänd plats som leder till Mass hämtning av filer**

- **Inloggnings händelse från en infekterad enhet som leder till Mass fil hämtning**

- **Inloggnings händelse från en anonym IP som leder till Mass hämtning av filer**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass nedladdning av filer**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Mass delning av filer efter misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, exfiltrering

**Mitre att&CK tekniker:** Giltigt konto (T1078), exfiltrering via webb tjänst (T1567)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett antal filer över ett visst tröskelvärde delats till andra efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet för att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att stjäla data från din organisations nätverk genom att dela filer, till exempel dokument, kalkyl blad osv. med obehöriga användare av skadliga orsaker. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen Mass fil delning är:  

- **Omöjlig resa till en ovanlig-plats som leder till Mass delning av filer**

- **Inloggnings händelse från en okänd plats som leder till Mass delning av filer**

- **Inloggnings händelse från en infekterad enhet som leder till Mass fil delning**

- **Inloggnings händelse från en anonym IP-adress som leder till Mass delning av filer**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass delning av filer**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Misstänkta regler för att ändra inkorgen efter misstänkt Azure AD-inloggning
Det här scenariot hör till två hot klassificeringar i den här listan: **data exfiltrering** och **lateral förflyttning**. För tydlighetens skull visas den i båda avsnitten.

Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, lateral förflyttning, exfiltrering

**Mitre att&CK tekniker:** Giltigt konto (T1078), intern spjut phishing (T1534)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen anger att regler för avvikande inkorg angavs i en användares inkorg efter en misstänkt inloggning till ett Azure AD-konto. Detta ger en hög exakthet-indikation om att kontot som anges i fusions incident beskrivningen har komprometterats och använts för att ändra användarens regler för e-postinkorg för skadliga syfte. Detta kan eventuellt vara ett försök av en angripare att stjäla data från organisationens nätverk. Alternativt kan angriparen försöka generera phishing-e-postmeddelanden från organisationen (kringgå funktioner för nätfiske-identifiering som riktas mot e-post från externa källor) i syfte att flytta senare genom att få åtkomst till ytterligare användar-och/eller privilegierade konton. Permutationerna för misstänkta Azure AD-inloggningar med varnings regler för misstänkt inkorg är:  

- **Omöjlig resa till en ovanlig plats som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en okänd plats som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt ändrings regel för Inkorgen**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Flera Power BI rapport delnings aktiviteter efter misstänkt Azure AD-inloggning 
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, exfiltrering 

**Mitre att&CK tekniker:** Giltigt konto (T1078), exfiltrering via webb tjänst (T1567)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal Power BI rapporter delades i en enda session efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att stjäla data från din organisations nätverk genom att dela Power BI rapporter med obehöriga användare i skadligt syfte. Permutationerna för misstänkta Azure AD-inloggningsnamn med flera Power BI rapport delnings aktiviteter är:  

- **Omöjlig resa till en ovanlig-plats som leder till flera Power BI rapport delnings aktiviteter**

- **Inloggnings händelse från en okänd plats som leder till flera Power BI rapport delnings aktiviteter**

- **Inloggnings händelse från en infekterad enhet som leder till flera Power BI rapport delnings aktiviteter**

- **Inloggnings händelse från en anonym IP-adress som leder till flera Power BI rapport delnings aktiviteter**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till flera Power BI rapport delnings aktiviteter**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Misstänkt Power BI rapport delning efter misstänkt Azure AD-inloggning
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, exfiltrering 

**Mitre att&CK tekniker:** Giltigt konto (T1078), exfiltrering via webb tjänst (T1567)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att en misstänkt Power BI rapport delnings aktivitet inträffat efter en misstänkt inloggning till ett Azure AD-konto. Delnings aktiviteten identifierades som misstänkt eftersom Power BI rapporten innehöll känslig information som identifierats med hjälp av naturligt språk bearbetning, och eftersom den delades med en extern e-postadress, publicerats på webben eller levererats som en ögonblicks bild till en externt prenumererande e-postadress. Den här varningen anger att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att stjäla känsliga data från din organisation genom att dela Power BI rapporter med obehöriga användare för skadliga orsaker. Permutationerna för misstänkta aviseringar om Azure AD-inloggningar med den misstänkta Power BI rapport delningen är:  

- **Omöjlig resa till en ovanlig-plats som leder till misstänkt Power BI rapport delning**

- **Inloggnings händelse från en okänd plats som leder till misstänkt Power BI rapport delning**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt Power BI rapport delning**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt Power BI rapport delning**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt Power BI rapport delning**

## <a name="data-destruction"></a>Data förstörelse

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Borttagning av Mass fil efter misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, påverkan

**Mitre att&CK tekniker:** Giltigt konto (T1078), data förstörelse (T1485)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal unika filer togs bort efter en misstänkt inloggning till ett Azure AD-konto. Detta ger en indikation på att kontot som anges i fusions incident beskrivningen kan ha komprometterats och använts för att förstöra data för skadliga syfte. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen Mass borttagning av Mass filer är:  

- **Omöjlig resa till en ovanlig plats som leder till Mass borttagning av filer**

- **Inloggnings händelse från en okänd plats som leder till Mass borttagning av filer**

- **Inloggnings händelse från en infekterad enhet som leder till Mass borttagning av filer**

- **Inloggnings händelse från en anonym IP-adress som leder till Mass borttagning av filer**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Mass borttagning av filer**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Misstänkt e-post borttagnings aktivitet efter misstänkt Azure AD-inloggning
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, påverkan 

**Mitre att&CK tekniker:** Giltigt konto (T1078), data förstörelse (T1485)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal e-postmeddelanden togs bort i en enda session efter en misstänkt inloggning till ett Azure AD-konto. Detta ger en indikation på att kontot som anges i fusions incident beskrivningen kan ha komprometterats och använts för att förstöra data för skadliga syfte, till exempel skada organisationen eller dölja skräp post. Permutationerna för misstänkta inloggnings aviseringar i Azure AD med aviseringen misstänkt borttagning av e-post är:   

- **Omöjlig resa till en ovanlig plats som leder till misstänkt e-postborttagnings aktivitet**

- **Inloggnings händelse från en okänd plats som leder till misstänkt e-postborttagnings aktivitet**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt e-postborttagnings aktivitet**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt e-postborttagnings aktivitet**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt borttagning av e-post**

## <a name="denial-of-service"></a>Denial of Service

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Flera virtuella dator borttagnings aktiviteter efter misstänkt Azure AD-inloggning
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, påverkan

**Mitre att&CK tekniker:** Giltigt konto (T1078), Endpoint denial of service (T1499)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal virtuella datorer togs bort i en enda session efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att försöka störa eller förstöra organisationens moln miljö. Permutationerna för misstänkta Azure AD-inloggningar med aviseringar för att ta bort flera virtuella datorer är:  

- **Omöjlig resa till en ovanlig-plats som leder till flera borttagnings aktiviteter för virtuella datorer**

- **Inloggnings händelse från en okänd plats som leder till flera borttagnings aktiviteter för virtuella datorer**

- **Inloggnings händelse från en infekterad enhet som leder till flera borttagnings aktiviteter för virtuella datorer**

- **Inloggnings händelse från en anonym IP-adress som leder till flera borttagnings aktiviteter för virtuella datorer**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till flera borttagnings aktiviteter för virtuella datorer**

## <a name="lateral-movement"></a>Sidorörelse

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Personifiering av Office 365 efter misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, lateral förflyttning

**Mitre att&CK tekniker:** Giltigt konto (T1078), intern spjut phishing (T1534)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal personifierings åtgärder inträffade efter en misstänkt inloggning från ett Azure AD-konto. I vissa program finns det alternativ för att tillåta användare att personifiera andra användare. E-posttjänster gör det till exempel möjligt för användarna att tillåta att andra användare skickar e-post för deras räkning. Den här varningen anger att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att utföra personifiering av skadligt syfte, till exempel skicka phishing-e-post för distribution av skadlig kod eller lateral förflyttning. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen Office 365-personifiering är:  

- **Omöjlig resa till en ovanlig-plats som leder till Office 365-personifiering**

- **Inloggnings händelse från en okänd plats som leder till Office 365-personifiering**

- **Inloggnings händelse från en infekterad enhet som leder till Office 365-personifiering**

- **Inloggnings händelse från en anonym IP-adress som leder till Office 365-personifiering**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till Office 365-personifiering**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Misstänkta regler för att ändra inkorgen efter misstänkt Azure AD-inloggning
Det här scenariot hör till två hot klassificeringar i den här listan: **lateral förflyttning** och **data exfiltrering**. För tydlighetens skull visas den i båda avsnitten.

Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, lateral förflyttning, exfiltrering

**Mitre att&CK tekniker:** Giltigt konto (T1078), intern spjut nätfiske (T1534), automatiserad exfiltrering (T1020)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen anger att regler för avvikande inkorg angavs i en användares inkorg efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att ändra användarens regler för e-postinkorg för skadliga syfte. Detta kan eventuellt vara ett försök av en angripare att stjäla data från organisationens nätverk. Alternativt kan angriparen försöka generera phishing-e-postmeddelanden från organisationen (kringgå funktioner för nätfiske-identifiering som riktas mot e-post från externa källor) i syfte att flytta senare genom att få åtkomst till ytterligare användar-och/eller privilegierade konton. Permutationerna för misstänkta Azure AD-inloggningar med varnings regler för misstänkt inkorg är:

- **Omöjlig resa till en ovanlig plats som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en okänd plats som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt ändrings regel för Inkorgen**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt ändrings regel för Inkorgen**

## <a name="malicious-administrative-activity"></a>Skadlig administrativ aktivitet

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Misstänkt administrativ app i molnet som följer misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, persistens, Försvarets skatteflykt, lateral förflyttning, insamling, exfiltrering och effekt

**Mitre att&CK tekniker:** EJ TILLÄMPLIGT

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att ett avvikande antal administrativa aktiviteter utfördes i en enda session efter en misstänkt Azure AD-inloggning från samma konto. Detta ger en indikation på att kontot som anges i fusions incident beskrivningen kan ha komprometterats och använts för att göra ett valfritt antal obehöriga administrativa åtgärder med skadlig avsikt. Det innebär också att ett konto med administratörs behörighet kan ha komprometterats. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen om den misstänkta moln appens administrativa aktivitet är:  

- **Omöjlig resa till en ovanlig plats som leder till misstänkt administrativ aktivitet i Cloud App**

- **Inloggnings händelse från en okänd plats som leder till misstänkt administrativ aktivitet i Cloud App**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt administrativ aktivitet i Cloud App**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt administrativ aktivitet i Cloud App**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt administrativ aktivitet i Cloud App**

## <a name="malicious-execution-with-legitimate-process"></a>Skadlig körning med en legitim process

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell gjorde en misstänkt nätverks anslutning, följt av avvikande trafik som flaggats av Palo-nätverkets brand vägg.
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Projektering

**Mitre att&CK tekniker:** Kommando-och skript tolkare (T1059)

**Data anslutnings källor:** Microsoft Defender för slut punkt (tidigare Microsoft Defender Advanced Threat Protection eller MDATP), Palo-nätverk 

**Beskrivning:** Fusions incidenter av den här typen indikerar att en utgående anslutningsbegäran gjordes via ett PowerShell-kommando och följer detta, vilket upptäcktes genom att avvikande inkommande aktivitet upptäcktes av brand väggen för Palo-nätverk. Detta ger en indikation på att en angripare har troligt vis fått åtkomst till nätverket och försöker utföra skadliga åtgärder. Anslutnings försök av PowerShell som följer det här mönstret kan vara en indikation på kommando-och kontroll aktivitet för skadlig kod, begär Anden om att hämta ytterligare skadlig kod eller en angripare som upprättar en fjärran sluten interaktiv åtkomst. Den här aktiviteten kan vara en legitim användning av PowerShell, precis som med alla "lever av" mark "-angrepp. Körningen av PowerShell-kommandot följt av misstänkt inkommande brand Väggs aktivitet ökar dock förtroendet att PowerShell används på ett skadligt sätt och bör undersökas vidare. I Palo-löpeld-loggar fokuserar Azure Sentinel på [hot loggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafiken betraktas som misstänkt när hot tillåts (misstänkta data, filer, överbelastningar, paket, genomsökningar, spionprogram, webb adresser, virus, sårbarheter,-virus, Wildfires). Referera också till den Palo-som motsvarar [hotet/innehålls typen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) som visas i beskrivningen av fusions incidenten för ytterligare information om aviseringar.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Misstänkt fjärr-WMI-körning följt av avvikande trafik som flaggats av Palo-nätverks brand vägg
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Körning, identifiering

**Mitre att&CK tekniker:** Windows Management Instrumentation (T1047)

**Data anslutnings källor:** Microsoft Defender för slut punkt (tidigare MDATP), Palo-nätverk 

**Beskrivning:** Fusions incidenter av den här typen indikerar att WMI-kommandon (Windows Management Interface) fjärrkördes i ett system, och efter det upptäcktes misstänkt inkommande aktivitet av Palo-nätverks brand väggen. Detta ger en indikation på att en angripare kan ha fått åtkomst till nätverket och försöker flytta senare, eskalera behörigheter och/eller köra skadliga nytto laster. Den här aktiviteten kan vara en legitim användning av WMI, precis som med alla "lever av" mark "-angrepp. Fjärrkörningen av WMI-kommandot följt av misstänkt inkommande brand Väggs aktivitet ökar dock säkerheten för att WMI används på ett skadligt sätt och bör undersökas vidare. I Palo-löpeld-loggar fokuserar Azure Sentinel på [hot loggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafiken betraktas som misstänkt när hot tillåts (misstänkta data, filer, överbelastningar, paket, genomsökningar, spionprogram, webb adresser, virus, sårbarheter,-virus, Wildfires). Referera också till den Palo-som motsvarar [hotet/innehålls typen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) som visas i beskrivningen av fusions incidenten för ytterligare information om aviseringar.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Misstänkt PowerShell-kommandorad efter misstänkt inloggning

**Mitre att&CK taktiker:** Första åtkomst, körning

**Mitre att&CK tekniker:** Giltigt konto (T1078), kommando-och skript tolk (T1059)

**Data anslutnings källor:** Azure Active Directory Identity Protection, Microsoft Defender för slut punkt (tidigare MDATP)

**Beskrivning:** Fusions incidenter av den här typen indikerar att en användare körde PowerShell-kommandon som kan vara skadliga efter en misstänkt inloggning till ett Azure AD-konto. Detta ger en hög exakthet-indikation om att kontot som anges i aviserings beskrivningen har komprometterats och ytterligare skadliga åtgärder vidtogs. Angripare utnyttjar ofta PowerShell för att köra skadliga nytto laster i minnet utan att lämna artefakter på disken, för att undvika identifiering av diskbaserade säkerhetsmekanismer som virus skannrar. Permutationerna för misstänkta Azure AD-inloggningsnamn med aviseringen om misstänkt PowerShell-kommando är:

- **Omöjlig resa till ovanlig-platser som leder till misstänkt PowerShell-kommandorad**

- **Inloggnings händelse från en okänd plats som leder till misstänkt PowerShell-kommandorad**

- **Inloggnings händelse från en infekterad enhet som leder till misstänkt PowerShell-kommandorad**

- **Inloggnings händelse från en anonym IP-adress som leder till misstänkt PowerShell-kommandorad**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till misstänkt PowerShell-kommandorad**

## <a name="malware-c2-or-download"></a>C2 eller hämtning av skadlig kod

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Nätverks förfrågan till TOR anonymisering-tjänsten följt av avvikande trafik som flaggats av Palo-nätverkets brand vägg.
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Kommando och kontroll

**Mitre att&CK tekniker:** Krypterad kanal (T1573), proxy (T1090)

**Data anslutnings källor:** Microsoft Defender för slut punkt (tidigare MDATP), Palo-nätverk 

**Beskrivning:** Fusions incidenter av den här typen indikerar att en utgående anslutningsbegäran gjordes till TOR anonymisering-tjänsten och följer detta, identifierade avvikande inkommande aktivitet av brand väggen för Palo-nätverks brand väggen. Detta ger en indikation på att en angripare har troligt vis fått åtkomst till nätverket och försöker dölja sina åtgärder och avsikter. Anslutningar till TOR-nätverket efter det här mönstret kan vara en indikation på kommando-och kontroll aktivitet för skadlig kod, begär Anden om att hämta ytterligare skadlig kod eller en angripare som upprättar en fjärran sluten interaktiv åtkomst. I Palo-löpeld-loggar fokuserar Azure Sentinel på [hot loggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafiken betraktas som misstänkt när hot tillåts (misstänkta data, filer, överbelastningar, paket, genomsökningar, spionprogram, webb adresser, virus, sårbarheter,-virus, Wildfires). Referera också till den Palo-som motsvarar [hotet/innehålls typen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) som visas i beskrivningen av fusions incidenten för ytterligare information om aviseringar.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Utgående anslutning till IP med en historik över obehöriga åtkomst försök följt av avvikande trafik som flaggats av Palo-nätverks brand vägg
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Kommando och kontroll

**Mitre att&CK tekniker:** Ej tillämpligt

**Data anslutnings källor:** Microsoft Defender för slut punkt (tidigare MDATP), Palo-nätverk 

**Beskrivning:** Fusions incidenter av den här typen indikerar att en utgående anslutning till en IP-adress med en historik över nekade åtkomst försök upprättades, och efter det upptäcktes avvikande aktivitet av Palo-nätverkets brand vägg. Detta ger en indikation på att en angripare har troligt vis fått åtkomst till nätverket. Anslutnings försök som följer detta mönster kan vara en indikation på kommando-och kontroll aktivitet för skadlig kod, begär Anden om att hämta ytterligare skadlig kod eller en angripare som upprättar en fjärran sluten interaktiv åtkomst. I Palo-löpeld-loggar fokuserar Azure Sentinel på [hot loggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafiken betraktas som misstänkt när hot tillåts (misstänkta data, filer, överbelastningar, paket, genomsökningar, spionprogram, webb adresser, virus, sårbarheter,-virus, Wildfires). Referera också till den Palo-som motsvarar [hotet/innehålls typen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) som visas i beskrivningen av fusions incidenten för ytterligare information om aviseringar.

## <a name="ransomware"></a>Utpressningstrojaner

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Utpressnings tro Jan-körning efter misstänkt Azure AD-inloggning

**Mitre att&CK taktiker:** Initial åtkomst, påverkan

**Mitre att&CK tekniker:** Giltigt konto (T1078), data som har krypterats för påverkan (T1486)

**Data anslutnings källor:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Beskrivning:** Fusions incidenter av den här typen indikerar att avvikande användar beteende som indikerar en utpressnings attack har upptäckts efter en misstänkt inloggning till ett Azure AD-konto. Den här indikationen ger hög exakthet att det konto som anges i fusions incident beskrivningen har komprometterats och använts för att kryptera data i syfte att extorting data ägaren eller neka data ägaren åtkomst till sina data. Permutationerna för misstänkta aviseringar i Azure AD-inloggning med utpressnings bara körnings aviseringar:  

- **Omöjlig resa till en ovanlig-plats som leder till utpressnings troprogram i Cloud App**

- **Inloggnings händelse från en okänd plats som leder till utpressnings troprogram i Cloud App**

- **Inloggnings händelse från en infekterad enhet som leder till utpressnings troprogram i Cloud App**

- **Inloggnings händelse från en anonym IP-adress som leder till utpressnings program vara i Cloud App**

- **Inloggnings händelse från användare med läckta autentiseringsuppgifter som leder till utpressnings program vara i Cloud App**

## <a name="remote-exploitation"></a>Fjärrhantering

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Misstänkt användning av angrepps ramverk följt av avvikande trafik som flaggats av Palo-nätverks brand vägg
Det här scenariot är för närvarande en för **hands version**.

**Mitre att&CK taktiker:** Initial åtkomst, körning, lateral förflyttning, behörighets eskalering

**Mitre att&CK tekniker:** Sårbarhet Public-Facing Application (T1190), utnyttjande för klient körning (T1203), utnyttjande av fjärrtjänster (T1210), utnyttjande av behörighets eskalering (T1068)

**Data anslutnings källor:** Microsoft Defender för slut punkt (tidigare MDATP), Palo-nätverk 

**Beskrivning:** Fusions incidenter av den här typen indikerar att protokoll som inte är standard använder, som liknar användningen av angrepps ramverk som Metasploit, har identifierats, och efter det att misstänkt inkommande aktivitet upptäcktes av brand väggen för Palo-. Detta kan vara en inledande indikation på att en angripare har utnyttjat en tjänst för att få åtkomst till dina nätverks resurser eller att en angripare redan har fått åtkomst och försöker ytterligare utnyttja tillgängliga system/tjänster för att flytta senare och/eller eskalera privilegier. I Palo-löpeld-loggar fokuserar Azure Sentinel på [hot loggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafiken betraktas som misstänkt när hot tillåts (misstänkta data, filer, överbelastningar, paket, genomsökningar, spionprogram, webb adresser, virus, sårbarheter,-virus, Wildfires). Referera också till den Palo-som motsvarar [hotet/innehålls typen](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) som visas i beskrivningen av fusions incidenten för ytterligare information om aviseringar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig mer om avancerad attack identifiering i flera steg kan du vara intresse rad av följande snabb start för att lära dig hur du får insyn i dina data och potentiella hot: [Kom igång med Azure Sentinel](quickstart-get-visibility.md).

Om du är redo att undersöka de incidenter som skapas för dig kan du läsa följande självstudie: [Undersök incidenter med Azure Sentinel](tutorial-investigate-cases.md).

