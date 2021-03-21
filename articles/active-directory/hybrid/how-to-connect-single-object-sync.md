---
title: 'Azure AD Connect synkronisering av enstaka objekt '
description: Lär dig hur du synkroniserar ett objekt från Active Directory till Azure AD för fel sökning.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726098"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect synkronisering av enstaka objekt 

Verktyget Azure AD Connect Sync (Single Object Sync) är en PowerShell-cmdlet som kan användas för att synkronisera ett enskilt objekt från Active Directory till Azure Active Directory. Rapporten som genereras kan användas för att undersöka och felsöka problem med att synkronisera varje objekt. 

> [!NOTE]
> Verktyget stöder synkronisering från Active Directory till Azure Active Directory. Den stöder inte synkronisering från Azure Active Directory till Active Directory. 
>
> Verktyget stöder synkronisering av objekt ändringar Lägg till och uppdatera. Den har inte stöd för att synkronisera en borttagning av objekt ändringar. 

## <a name="how-it-works"></a>Så här fungerar det
Verktyget för synkronisering av enstaka objekt kräver ett Active Directory unikt namn som indatamängds för att hitta käll anslutningen och partitionen för import. Den exporterar ändringarna till Azure Active Directory. Verktyget genererar ett JSON-utdata som liknar resurs typen **provisioningObjectSummary** . 

Verktyget för synkronisering av enstaka objekt utför följande steg: 

 1. Kontrol lera om objektets (käll domän) domän (Active Directory anslutning och partition) i Sync-omfånget. 
 2. Kontrol lera om objektets (mål) domän (Azure Active Directory koppling och partition) i Sync-omfånget. 
 3. Avgör om objektets organisationsenhet i Sync-omfånget. 
 4. Avgör om objektet kan nås med autentiseringsuppgifter för kopplings kontot. 
 5. Avgör om objekt typen i Sync-omfånget. 
 6. Avgör om objektet är i Sync-omfånget om grupp filtrering är aktiverat. 
 7. Importera objekt från Active Directory till Active Directory kopplings utrymme. 
 8. Importera objekt från Azure Active Directory till Azure Active Directory kopplings utrymme. 
 9. Synkronisera objekt från Active Directory kopplings utrymme. 
 10. Exportera objekt från Azure Active Directory anslutnings utrymme till Azure Active Directory. 

Förutom JSON-utdata genererar verktyget en HTML-rapport som innehåller all information om synkroniseringsåtgärden. HTML-rapporten finns i **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm**. Den här HTML-rapporten kan delas med support teamet för ytterligare fel sökning, om det behövs. 

HTML-rapporten har följande: 

|Flik|Beskrivning|
|-----|-----|
|Steg|Beskriver de steg som vidtas för att synkronisera ett objekt. Varje steg innehåller information om fel sökning. Stegen importera, synkronisera och exportera innehåller ytterligare attributinformation som namn, är flera värden, typ, värde, värde Lägg till, värde Delete, operation, Synkroniseringsregel, mappnings typ och data källa.| 
|Felsöka & rekommendation|Innehåller felkod och orsak. Fel informationen är bara tillgänglig om ett fel inträffar.| 
|Ändrade egenskaper|Visar det gamla värdet och det nya värdet. Om det inte finns något gammalt värde eller om det nya värdet tas bort, är cellen tom. För attribut med flervärdesattribut visas antalet. Attributnamnet är en länk till fliken steg: Exportera objekt från Azure Active Directory anslutnings utrymme till Azure Active Directory: attributinformation som innehåller ytterligare information om attributet, till exempel namn, är flera värden, typ, värde, värde Lägg till, värde borttagning, åtgärd, Synkroniseringsregel, mappnings typ och data källa.| 
|Sammanfattning|Innehåller en översikt över vad som hände och identifierare för objektet i käll-och mål systemen.| 

## <a name="prerequisites"></a>Förutsättningar 

För att kunna använda Sync-verktyget för enstaka objekt måste du använda 2021 mars-versionen av Azure AD Connect eller senare. 

### <a name="run-the-single-object-sync-tool"></a>Kör verktyget för synkronisering av enstaka objekt 

Utför följande steg för att köra verktyget för synkronisering av enstaka objekt: 

 1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet Kör som administratör. 

 2. Ange [körnings principen](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) till RemoteSigned eller obegränsad. 

 3. Inaktivera Schemaläggaren när du har verifierat att inga synkroniseringsproblem körs. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importera modulen AdSync-diagnostik 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Anropa cmdleten Single Object Sync. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Återaktivera synkroniseringen av Schemaläggaren. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Indataparametrar för synkronisering av enstaka objekt|Beskrivning| 
|-----|----|
|DistinguishedName|Detta är en obligatorisk sträng parameter. </br></br>Detta är Active Directory objektets unika namn som behöver synkronisering och fel sökning.| 
|StagingMode|Detta är en valfri växel parameter.</br></br>Den här parametern kan användas för att förhindra export av ändringar till Azure Active Directory.</br></br>**Obs**: cmdleten utför synkroniseringsåtgärden. </br></br>**Obs!** Azure AD Connect-mellanlagringsplatsen kommer inte att exportera ändringarna till Azure Active Directory.|
|NoHtmlReport|Detta är en valfri växel parameter.</br></br>Den här parametern kan användas för att förhindra att HTML-rapporten genereras. 

## <a name="single-object-sync-throttling"></a>Begränsning av synkronisering av enstaka objekt 

Verktyget för synkronisering av enstaka objekt **är** avsett för att undersöka och felsöka problem med att synkronisera varje objekt. Den är **inte** avsedd att ersätta den synkronisering som körs av Scheduler. Import från Azure Active Directory och export till Azure Active Directory omfattas av begränsningar för begränsning. Försök igen om 5 minuter, om du når begränsnings gränsen. 

## <a name="next-steps"></a>Nästa steg
- [Felsöka objekt synkronisering](tshoot-connect-objectsync.md)
- [Felsöka objekt som inte synkroniseras](tshoot-connect-object-not-syncing.md)
- [Fel sökning från slut punkt till slut punkt för Azure AD Connect objekt och attribut](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
