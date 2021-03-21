---
title: Azure AD Connect Sync v2-slutpunkt | Microsoft Docs
description: 'Det här dokumentet täcker uppdateringar av API: t för Azure AD Connect-synkronisering v2-slutpunkt.'
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
ms.openlocfilehash: 0ecfd277f2cc86102d59b201e7b43fa8519bdd3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937611"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Slutpunkts-API för Azure AD Connect-synkronisering v2 
Microsoft har distribuerat en ny slut punkt (API) för Azure AD Connect som förbättrar prestandan för synkroniseringstjänsten i Azure Active Directory. Genom att använda den nya v2-slutpunkten får du märkbara prestanda vinster vid export och import till Azure AD. Den här nya slut punkten stöder följande:
    
 - Synkronisera grupper med upp till 250 000 medlemmar
 - prestanda vinster vid export och import till Azure AD
 
> [!NOTE]
> För närvarande har den nya slut punkten ingen konfigurerad grupp storleks gräns för Microsoft 365 grupper som skrivs tillbaka. Detta kan påverka din Active Directory och fördröjning av synkronisering. Vi rekommenderar att du ökar grupp storlekarna stegvis.  

## <a name="prerequisites"></a>Förutsättningar  
För att du ska kunna använda den nya v2-slutpunkten måste du använda [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare och följa distributions stegen nedan för att aktivera v2-slutpunkten för din Azure AD Connect-Server.   

## <a name="deployment-guidance"></a>Vägledning för distribution 
Du måste distribuera [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare för att kunna använda v2-slutpunkten. Använd länken som finns för att ladda ned. 

Vi rekommenderar att du följer [migrations](./how-to-upgrade-previous-version.md#swing-migration) metoden för att flytta ut den nya slut punkten i din miljö. Detta ger en tydlig katastrof plan i händelse av att det krävs en större återställning. I följande exempel visas hur en sväng migrering kan användas i det här scenariot. Mer information om distributions metoden för att flytta migreringen finns i den angivna länken. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Flytta migrering för distribution v2-slutpunkt
Följande steg vägleder dig genom distributionen av v2-slutpunkten med hjälp av metoden sväng.

1. Distribuera v2-slutpunkten på den aktuella Staging-servern. Den här servern kommer att kallas **v2-servern** i stegen nedan. Den aktuella aktiva servern fortsätter att bearbeta produktions belastningen med hjälp av v1-slutpunkten, som kommer att kallas **v1-servern** nedan.
1. Kontrol lera att **v2-servern** fortfarande bearbetar importen som förväntat. I det här skedet allokeras inte stora grupper till Azure AD eller lokal AD, men du kan kontrol lera att uppgraderingen inte resulterade i någon annan oväntad påverkan på den befintliga synkroniseringsprocessen. 
2. När verifieringen är klar växlar du **v2-servern** så att den är den aktiva servern och **v1-servern** som ska vara mellanlagrings Server. För närvarande kommer stora grupper som är inom omfånget att synkroniseras att tillhandahållas till Azure AD, och även stora Microsoft 365 enhetliga grupper tillhandahålls till AD, om tillbakaskrivning av grupp är aktiverat.
3. Verifiera att **v2-servern** utför och bearbetar stora grupper korrekt. Du kan välja att stanna kvar i det här steget och övervaka synkroniseringsprocessen för en period.
  >[!NOTE]
  > Om du behöver övergå tillbaka till din tidigare konfiguration kan du utföra en flyttning från **v2-servern** till **v1-servern**. Eftersom v1-slutpunkten inte stöder grupper med över 50 000 medlemmar, kommer alla stora grupper som etablerades av Azure AD Connect, i antingen Azure AD eller lokal AD, att tas bort. 
4. När du är säker på att använda v2-slutpunkten uppgraderar du **v1-servern** för att börja använda v2-slutpunkten. 
 

## <a name="expectations-of-performance-impact"></a>Förväntningar på prestanda påverkan  
När du använder v2-slutpunkten är prestanda vinster en funktion i antalet synkroniserade grupper, storleken på grupperna och deras grupp omsättning (den aktivitet som resulterade i att lägga till och ta bort användare som medlemmar i gruppen). Om du använder den nya slut punkten utan att öka antalet, storleken eller omsättningen för de synkroniserade grupperna bör det leda till kortare tider för export och import till Azure AD. 
 
Prestanda vinster kan dock negeras av den ytterligare bearbetning som krävs när stora grupper synkroniseras. Du kan öka den totala synkroniserings tiden genom att lägga till en för många stora grupper i synkroniseringsprocessen.  

För att få en bättre förståelse för hur tillägget av de nya grupperna påverkar dina synkroniseringsinställningar rekommenderar vi att du börjar genom att bara synkronisera några få stora grupper med färre än 100 000 medlemmar. Du kan sedan öka antalet och storleken på grupper genom att placera mer av dem i omfång, via OU, attribut eller Max grupp storleks filtrering. Prestanda förbättringarna kommer att realiseras på export-och import aktiviteterna för Azure AD Connector, inte på den lokala AD-anslutningen. 

## <a name="deployment-step-by-step"></a>Distribution steg för steg 
Följande tre faser är ett djupgående exempel på hur du distribuerar den nya v2-slutpunkten.  Använd faserna som en rikt linje för distributionen.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fas 1 – Installera och verifiera Azure AD Connect 
Vi rekommenderar att du först utför stegen för att installera eller uppgradera till [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare och verifiera synkroniseringsprocessen innan du går till den andra fasen där du ska aktivera v2-slutpunkten. På Azure AD Connect-servern: 


1. Valfritt Ta säkerhets kopia av databas 
2. Installera eller uppgradera till [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) eller senare.
3. Verifiera installationen 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fas 2 – aktivera v2-slutpunkten 
Nästa steg är att aktivera v2-slutpunkten. 

> [!NOTE]
> När du har aktiverat v2-slutpunkten för servern kommer du att kunna se vissa prestanda förbättringar för din befintliga arbets belastning. Du kommer inte att kunna synkronisera grupper med fler som 50 000 medlemmar. 

Använd följande steg för att växla till v2-slutpunkten: 

1. Öppna en PowerShell-prompt som administratör. 
2. Inaktivera Schemaläggaren när du har verifierat att inga synkroniseringsproblem körs: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importera den nya modulen: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Växla till v2-slut punkten:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Du har nu aktiverat v2-slutpunkten för servern. Det tar lite tid att kontrol lera att det inte finns några oväntade resultat efter aktivering av v2-slutpunkten innan du går vidare till nästa fas där du ökar grupp storleks gränsen. 
>[!NOTE]
>Sökvägen till filen/modulen kan använda en annan enhets beteckning, beroende på vilken installations Sök väg som angavs när du installerade Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fas 3 – öka grupp medlemskaps gränsen 
När du har kontrollerat att tjänsten körs utan oväntade resultat kan du fortsätta att höja gränsen för grupp medlemskap. Vi rekommenderar att först öka medlemskaps gränsen till ett något högre värde, e g. 75K-medlemmar för att se de större grupperna som synkroniseras med Azure AD. När du är nöjd med resultaten kan du öka medlems gränsen ytterligare.  

Den maximala gränsen är 250 000 medlemmar per grupp. 

Följande steg kan användas för att öka medlemskaps gränsen:  

1. Öppna redigeraren för Azure AD-synkronisering 
2. I redigeraren väljer du **utgående** för riktning 
3. Klicka på synkroniseringsregeln **till AAD – grupp koppling** 
4. Klicka på  knappen Redigera ![ skärm bild som visar alternativet "Visa och hantera regler för synkronisering" med "ut till AAD-Group Join".](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Klicka på **Ja** om du vill inaktivera standard regeln och skapa en redigerbar kopia.
 ![Skärm bild som visar fönstret "redigera reserverad regel bekräftelse" med knappen "Ja" vald.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. I popup-fönstret på sidan **Beskrivning** anger du prioriteten till ett tillgängligt värde mellan 1 och 99 ![ skärm bild som visar fönstret "Redigera regel för utgående synkronisering" med "prioritet" markerat.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. På sidan **omvandlingar** uppdaterar du **käll** värdet för **medlems** omvandlingen, ersätter "50000" med ett värde mellan 50001 och 250000. Den här ersättningen ökar den maximala storleken för grupper som ska synkroniseras till Azure AD. Vi rekommenderar att du börjar med ett antal 100 000 för att förstå vilken effekt som synkroniserar stora grupper med dina synkroniseringsinställningar. 
 
 **Exempel** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Redigera Synch-regel](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klicka på Spara 
10. Öppna admin PowerShell-prompt 
11. Återaktivera synkroniseringen av Schemaläggaren 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Om Azure AD Connect Health inte har Aktiver ATS ändrar du inställningarna för Windows program händelse logg för att arkivera loggarna, i stället för att skriva över dem. Loggarna kan användas för att under lätta framtida fel söknings åtgärder. 

>[!NOTE]
> När du har aktiverat den nya slut punkten kan du se ytterligare export fel på AAD-anslutningen med namnet DN-attributes-Failure. Det kommer att finnas en motsvarande händelse logg post för varje fel med ID 6949. Felen är information och indikerar inte något problem med installationen, utan i stället kan synkroniseringen inte lägga till vissa medlemmar i en grupp i Azure AD eftersom själva medlems objektet inte har synkroniserats med Azure AD. 

Den nya v2-slutpunkts koden hanterar vissa typer av export fel något annorlunda än v1-koden.  Du kan se mer av informations fel meddelandena när du använder v2-slutpunkten. 

>[!NOTE]
> När du uppgraderar Azure AD Connect ska du se till att stegen i fas 2 körs igen, eftersom ändringarna inte bevaras genom uppgraderings processen. 

Vid efterföljande ökningar till grupp medlems gränsen i regeln **out to AAD – grupp koppling** är en fullständig synkronisering inte nödvändig, så du kan välja att utelämna den fullständiga synkroniseringen genom att köra följande kommando i PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Om du har Microsoft 365 enhetliga grupper som har fler än 50 000 medlemmar, kommer grupperna att läsas in i Azure AD Connect och om tillbakaskrivning av grupp är aktiverat skrivs de till din lokala AD. 

## <a name="rollback"></a>Återställning 
Om du har aktiverat v2-slutpunkten och behöver återställa, följer du dessa steg: 

1. På Azure AD Connect-servern: a. Valfritt Ta säkerhets kopia av databas 
2. Öppna en admin PowerShell-prompt:
3. Inaktivera Schemaläggaren när du har verifierat att inga synkroniseringsproblem körs
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Växla till v1-slutpunkten * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Öppna redigeraren för Azure AD-synkronisering 
6. Ta bort den redigerbara kopian av synkroniseringsregeln **till AAD – grupp kopplingens** Synkroniseringsregel 
7. Aktivera standard kopian av synkroniseringsregeln **till AAD – grupp kopplingens** Synkroniseringsregel 
8. Öppna en admin PowerShell-prompt 
9. Återaktivera synkroniseringen av Schemaläggaren 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> När du växlar tillbaka från v2 till v1-slutpunkter kommer grupper som synkroniserats med fler än 50 000-medlemmar att tas bort när en fullständig synkronisering har körts, för både AD-grupper som har tilldelats till Azure AD och Microsoft 365 enhetliga grupper som har skapats till AD. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar  
 
**När kommer den nya slut punkten att bli standard för uppgraderingar och nya installationer?**  
</br>Vi planerar en ny version av AADConnect som ska publiceras för hämtning i februari 2021. Den här versionen använder slut punkten v2 som standard och aktiverar synkronisering av grupper som är större än 50 000 utan ytterligare konfiguration. Den här versionen kommer senare att publiceras för automatisk uppgradering till berättigade servrar.
 
## <a name="next-steps"></a>Nästa steg

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
