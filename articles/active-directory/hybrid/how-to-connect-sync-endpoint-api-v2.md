---
title: Azure AD Connect V2-slutpunkt | Microsoft Docs
description: Det här dokumentet beskriver uppdateringar av API:et för Azure AD Connect Sync v2-slutpunkter.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575725"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Slutpunkts-API för Azure AD Connect-synkronisering v2 
Microsoft har distribuerat en ny slutpunkt (API) för Azure AD Connect som förbättrar prestanda för synkroniseringstjänstens åtgärder för att Azure Active Directory. Genom att använda den nya V2-slutpunkten får du märkbara prestandaförbättringar vid export och import till Azure AD. Den här nya slutpunkten stöder följande:
    
 - synkronisera grupper med upp till 250 000 medlemmar
 - prestandaförbättringar vid export och import till Azure AD
 
> [!NOTE]
> Den nya slutpunkten har för närvarande ingen konfigurerad storleksgräns för grupper Microsoft 365 grupper som skrivs tillbaka. Detta kan påverka active directory- och synkroniseringscykelns svarstider. Vi rekommenderar att du ökar gruppstorlekarna inkrementellt.  

>[!NOTE]
> Slutpunkts-API:Azure AD Connect V2 är för närvarande endast tillgängligt i följande Azure-miljöer:
> - Azure Commercial
> - Azure China-moln
> - Azure US Government-moln Det görs inte tillgängligt i Det tyska Azure-molnet

## <a name="prerequisites"></a>Förutsättningar  
För att kunna använda den nya V2-slutpunkten måste du använda [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare och följa distributionsstegen nedan för att aktivera V2-slutpunkten för din Azure AD Connect-server.   

## <a name="deployment-guidance"></a>Distributionsvägledning 
Du måste distribuera Azure AD Connect [version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare för att använda V2-slutpunkten. Använd länken för att ladda ned. 

Vi rekommenderar att du följer [swingmigreringsmetoden](./how-to-upgrade-previous-version.md#swing-migration) för att distribuera den nya slutpunkten i din miljö. Detta ger en tydlig reservplan i händelse av att en större återställning krävs. I följande exempel visas hur en swingmigrering kan användas i det här scenariot. Mer information om distributionsmetoden för swingmigrering finns i länken. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Swing-migrering för distribution av V2-slutpunkt
Följande steg vägleder dig genom distributionen av v2-slutpunkten med swingmetoden.

1. Distribuera V2-slutpunkten på den aktuella mellanlagringsservern. Den här servern kallas **V2-servern** i stegen nedan. Den aktuella aktiva servern fortsätter att bearbeta produktionsarbetsbelastningen med V1-slutpunkten, som kallas **V1-servern** nedan.
1. Kontrollera att **V2-servern** fortfarande bearbetar importer som förväntat. I det här skedet kommer stora grupper inte att etableras till Azure AD eller en on-prem AD, men du kommer att kunna verifiera att uppgraderingen inte resulterade i någon annan oväntad påverkan på den befintliga synkroniseringsprocessen. 
2. När verifieringen är klar växlar du **V2-servern** till den aktiva servern och **V1-servern till** mellanlagringsservern. För tiden etableras stora grupper som är i omfånget som ska synkroniseras till Azure AD, samt stora Microsoft 365 enhetliga grupper etableras till AD om tillbakaskrivning av grupper är aktiverat.
3. Kontrollera att **V2-servern** utför och bearbetar stora grupper. Du kan välja att stanna kvar i det här steget och övervaka synkroniseringsprocessen under en period.
  >[!NOTE]
  > Om du behöver gå tillbaka till din tidigare konfiguration kan du utföra en swingmigrering från **V2-servern** tillbaka till **V1-servern**. Eftersom V1-slutpunkten inte har stöd för grupper med fler än 50 000 medlemmar tas alla stora grupper som etablerades av Azure AD Connect, antingen i Azure AD eller i en on-prem AD, bort. 
4. När du är säker på att du använder V2-slutpunkten uppgraderar du **V1-servern för** att börja använda V2-slutpunkten. 
 

## <a name="expectations-of-performance-impact"></a>Förväntningar på prestandapåverkan  
När du använder V2-slutpunkten är prestandaförbättringar en funktion för antalet synkroniserade grupper, storleken på dessa grupper och deras gruppomsättning (aktiviteten som är resultatet av att lägga till och ta bort användare som medlemmar i gruppen). Om du använder den nya slutpunkten, utan att öka antalet, storleken eller omsättningen för de synkroniserade grupperna, bör det resultera i kortare tider för export och import till Azure AD. 
 
Prestandaförbättringarna kan dock negeras av den ytterligare bearbetning som krävs vid synkronisering av stora grupper. Du kan till slut öka den totala synkroniseringstiden genom att lägga till för många stora grupper i synkroniseringsprocessen.  

För att få en bättre förståelse för hur tillägg av de nya grupperna påverkar synkroniseringsprestandan rekommenderar vi att du börjar med att synkronisera bara några få stora grupper med färre än 100 000 medlemmar. Du kan sedan öka antalet och storleken på grupper genom att ta in fler av dem i omfånget, via ORGANISATIONSenhet, attribut eller maximal filtrering av gruppstorlek. Prestandaförbättringarna kommer att realiseras på export- och importuppgifterna för Azure AD-anslutningsappen, inte den lokala AD-anslutningsappen. 

## <a name="deployment-step-by-step"></a>Distribution steg för steg 
Följande tre faser är ett djupgående exempel på hur du distribuerar den nya V2-slutpunkten.  Använd faserna som riktlinjer för distributionen.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fas 1 – Installera och verifiera Azure AD Connect 
Vi rekommenderar att du först utför stegen för att installera eller uppgradera [till Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare och validera synkroniseringsprocessen innan du går till den andra fasen där du aktiverar V2-slutpunkten. På Azure AD Connect servern: 


1. [Valfritt] Säkerhetskopiera databasen 
2. Installera eller uppgradera till [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare.
3. Verifiera installationen 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fas 2 – Aktivera V2-slutpunkten 
Nästa steg är att aktivera V2-slutpunkten. 

> [!NOTE]
> När du har aktiverat V2-slutpunkten för servern kan du se vissa prestandaförbättringar för din befintliga arbetsbelastning. Du kommer dock inte att kunna synkronisera grupper med fler än 50 000 medlemmar ännu. 

Använd följande steg för att växla till V2-slutpunkten: 

1. Öppna en PowerShell-prompt som administratör. 
2. Inaktivera synkroniseringsschemat när du har verifierat att inga synkroniseringsåtgärder körs: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importera den nya modulen: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Växla till v2-slutpunkten:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Nu har du aktiverat V2-slutpunkten för servern. Ta lite tid att kontrollera att det inte finns några oväntade resultat efter aktivering av V2-slutpunkten innan du går vidare till nästa fas där du ökar gruppstorleksgränsen. 
>[!NOTE]
>Fil-/modulsökvägarna kan använda en annan enhetsbeteckning, beroende på vilken installationssökväg som anges när du Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fas 3 – Öka gruppmedlemskapsgränsen 
När du har kontrollerat att tjänsten körs utan oväntade resultat kan du fortsätta med att höja gränsen för gruppmedlemskap. Vi rekommenderar att du först höjer medlemskapsgränsen till ett något högre värde, t.ex. 75 000 medlemmar för att se de större grupperna som synkroniseras med Azure AD. När du är nöjd med resultaten kan du höja medlemsgränsen ytterligare.  

Maxgränsen är 250 000 medlemmar per grupp. 

Följande steg kan användas för att öka medlemskapsgränsen:  

1. Öppna Redigeraren för synkroniseringsregler i Azure AD 
2. I redigeraren väljer du **Utgående** för Riktning 
3. Klicka på **synkroniseringsregeln Out to AAD – Group Join (Ut** till AAD – gruppkoppling) 
4. Klicka på **knappen** Redigera Skärmbild som visar "Visa och hantera synkroniseringsregler" med "Ut till ![ AAD – Gruppkoppling" markerat.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Klicka på **knappen Ja** för att inaktivera standardregeln och skapa en redigerbar kopia.
 ![Skärmbild som visar fönstret "Redigera bekräftelse av reserverad regel" med knappen "Ja" markerad.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. I popup-fönstret på  sidan Beskrivning anger du prioriteten till ett tillgängligt värde mellan 1 och 99 Skärmbild som visar fönstret "Redigera utgående synkroniseringsregel" med ![ "Prioritet" markerat.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. På sidan **Transformationer** uppdaterar  du källvärdet för medlemstransformering och ersätter "50000&quot; med ett värde mellan 50001 och 250000.  Den här ersättningen ökar den maximala medlemskapsstorleken för grupper som ska synkroniseras med Azure AD. Vi rekommenderar att du börjar med ett antal 100 000 för att förstå hur synkronisering av stora grupper påverkar synkroniseringsprestandan. 
 
 **Exempel** 
 
 `IIF((ValueCount(&quot;member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Redigera synkroniseringsregel](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klicka på Spara 
10. Öppna PowerShell-kommandotolken för administratör 
11. Återaktivera synkroniseringsschemat 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Om Azure AD Connect Health inte är aktiverat ändrar du inställningarna för Windows-programmets händelselogg för att arkivera loggarna i stället för att skriva över dem. Loggarna kan användas för framtida felsökning. 

>[!NOTE]
> När du har aktivera den nya slutpunkten kan du se ytterligare exportfel på AAD-anslutningsappen med namnet "dn-attributes-failure". Det kommer att finnas en motsvarande händelseloggpost för varje fel med ID 6949. Felen är informations- och indikerar inte något problem med installationen, utan snarare att synkroniseringsprocessen inte kunde lägga till vissa medlemmar i en grupp i Azure AD eftersom själva medlemsobjektet inte synkroniserades till Azure AD. 

Den nya V2-slutpunktskoden hanterar vissa typer av exportfel som skiljer sig något från hur V1-koden gjorde.  Du kan se fler informationsfelmeddelanden när du använder V2-slutpunkten. 

>[!NOTE]
> När du Azure AD Connect måste du se till att stegen i Fas 2 omkörs, eftersom ändringarna inte bevaras under uppgraderingsprocessen. 

Vid efterföljande ökningar av gruppmedlemsgränsen i synkroniseringsregeln **Out to AAD – Group Join** (Ut till AAD – Gruppkoppling) behövs ingen fullständig synkronisering, så du kan välja att ignorera den fullständiga synkroniseringen genom att köra följande kommando i PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Om du har Microsoft 365 enhetliga grupper som har fler än 50 000 medlemmar läses grupperna in i Azure AD Connect, och om tillbakaskrivning av grupper är aktiverat skrivs de till din lokala AD. 

## <a name="rollback"></a>Återställning 
Om du har aktiverat v2-slutpunkten och behöver återställa följer du dessa steg: 

1. På Azure AD Connect server: a. [Valfritt] Säkerhetskopiera databasen 
2. Öppna en PowerShell-administratörsfråga:
3. Inaktivera synkroniseringsschemaläggaren när du har verifierat att inga synkroniseringsåtgärder körs
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Växla till V1-slutpunkten * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Öppna Redigeraren för synkroniseringsregler i Azure AD 
6. Ta bort den redigerbara kopian **av synkroniseringsregeln Ut till AAD – Gruppkoppling** 
7. Aktivera standardkopian av **synkroniseringsregeln Ut till AAD – Gruppkoppling** 
8. Öppna en PowerShell-administratörsfråga 
9. Återaktivera synkroniseringsschemat 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> När du växlar tillbaka från V2-till V1-slutpunkterna tas grupper som synkroniserats med fler än 50 000 medlemmar bort när en fullständig synkronisering har körts för både AD-grupper som etablerats till Azure AD och Microsoft 365 enhetliga grupper som etablerats till AD. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar  
 
**När blir den nya slutpunkten standard för uppgraderingar och nya installationer?**  
</br>Vi planerar att publicera en ny version av AADConnect för nedladdning i februari 2021. Den här versionen använder V2-startpunkten som standard och aktiverar synkroniseringsgrupper som är större än 50 000 utan någon ytterligare konfiguration. Den här versionen kommer senare att publiceras för automatisk uppgradering till berättigade servrar.
 
## <a name="next-steps"></a>Nästa steg

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
