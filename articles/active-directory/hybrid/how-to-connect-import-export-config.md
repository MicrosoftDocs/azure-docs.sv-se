---
title: Så här importerar och exporterar du Azure AD Connect konfigurations inställningar
description: I den här artikeln beskrivs vanliga frågor om moln etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681963"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Importera och exportera Azure AD Connect konfigurations inställningar 

Azure Active Directory (Azure AD) Connect-distributioner varierar från en installation av en enda skogs Express läge till komplexa distributioner som synkroniseras över flera skogar med hjälp av anpassade regler för synkronisering. På grund av det stora antalet konfigurations alternativ och-mekanismer är det viktigt att förstå vilka inställningar som gäller och snabbt kunna distribuera en server med en identisk konfiguration. Med den här funktionen kan du katalogisera konfigurationen av en specifik synkroniseringstjänst och importera inställningarna till en ny distribution. Olika ögonblicks bilder av synkroniseringsinställningar kan jämföras för att enkelt visualisera skillnaderna mellan två servrar, eller samma server över tid.

Varje gången som konfigurationen ändras från Azure AD Connects guiden exporteras en ny tids stämplings fil med JSON-inställningar automatiskt till **%programdata%\AADConnect**. Namnet på inställnings filen har formatet **tillämpad-SynchronizationPolicy-*. JSON**, där den sista delen av fil namnet är en tidstämpel.

> [!IMPORTANT]
> Endast ändringar som görs av Azure AD Connect exporteras automatiskt. Ändringar som görs med hjälp av PowerShell, Synchronization Service Manager eller redigeraren för Synkroniseringsregel måste exporteras vid behov för att underhålla en uppdaterad kopia. Export på begäran kan också användas för att placera en kopia av inställningarna på en säker plats för haveri beredskap.

## <a name="export-azure-ad-connect-settings"></a>Exportera Azure AD Connect inställningar 

Om du vill visa en sammanfattning av konfigurations inställningarna öppnar du Azure AD Connect-verktyget och väljer den ytterligare uppgiften med namnet **Visa eller exportera aktuell konfiguration**. En snabb sammanfattning av dina inställningar visas tillsammans med möjligheten att exportera den fullständiga konfigurationen av servern.

Som standard exporteras inställningarna till **%programdata%\AADConnect**. Du kan också välja att spara inställningarna på en skyddad plats för att säkerställa tillgängligheten om en katastrof inträffar. Inställningarna exporteras med JSON-filformat och ska inte skapas eller redige ras manuellt för att säkerställa logisk konsekvens. Det finns inte stöd för att importera en manuellt skapad eller redige rad fil och det kan leda till oväntade resultat.

## <a name="import-azure-ad-connect-settings"></a>Importera Azure AD Connect inställningar

Importera tidigare exporterade inställningar:
 
1. Installera **Azure AD Connect** på en ny server.
1. Välj alternativet **Anpassa** efter **välkomst** sidan.
1. Välj **Inställningar för att importera synkronisering**. Bläddra till filen med tidigare exporterade JSON-inställningar.
1. Välj **installera**.

   ![Skärm bild som visar skärmen installera nödvändiga komponenter](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Åsidosätt inställningar på den här sidan som att använda SQL Server i stället för LocalDB eller använda ett befintligt tjänst konto i stället för ett standard-VSA. De här inställningarna importeras inte från filen med konfigurations inställningar. De finns där för information och jämförelse.

### <a name="import-installation-experience"></a>Importera installations miljö 

Import installations upplevelsen är avsiktligt Sparad med minimala indata från användaren för att enkelt tillhandahålla reproducerbarhet för en befintlig server.

Här är de enda ändringar som kan göras under installationen. Alla andra ändringar kan göras efter installationen från guiden Azure AD Connect:
- **Azure Active Directory autentiseringsuppgifter**: konto namnet för den globala Azure-administratören som används för att konfigurera den ursprungliga servern föreslås som standard. Det *måste*   ändras om du vill synkronisera information till en ny katalog.
- **Användar inloggning**: de inloggnings alternativ som kon figurer ATS för den ursprungliga servern väljs som standard och automatiskt fråga efter autentiseringsuppgifter eller annan information som behövs under konfigurationen. I sällsynta fall kan det finnas behov av att konfigurera en server med olika alternativ för att undvika att ändra beteendet för den aktiva servern. Annars väljer du **Nästa** för att använda samma inställningar.
- **Autentiseringsuppgifter för lokal katalog**: för varje lokal katalog som ingår i dina synkroniseringsinställningar måste du ange autentiseringsuppgifter för att skapa ett synkroniseringsschema eller ange ett förskapat konto för anpassad synkronisering. Den här proceduren är identisk med den rena installations upplevelsen med undantaget att du inte kan lägga till eller ta bort kataloger.
- **Konfigurations alternativ**: som en ren installation kan du välja att konfigurera de ursprungliga inställningarna för om du vill starta automatisk synkronisering eller aktivera mellanlagrings läge. Den största skillnaden är att mellanlagrings läget är avsiktligt aktiverat som standard för att tillåta jämförelse av konfigurations-och synkroniseringsresultat innan du aktivt exporterar resultaten till Azure.

![Skärm bild som visar skärmen Anslut dina kataloger](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Endast en synkroniseringstjänst kan vara i den primära rollen och aktivt exportera konfigurations ändringar till Azure. Alla andra servrar måste placeras i mellanlagrings läge.

## <a name="migrate-settings-from-an-existing-server"></a>Migrera inställningar från en befintlig server 

Om en befintlig server inte stöder hantering av inställningar kan du antingen välja att uppgradera servern på plats eller migrera inställningarna för användning på en ny mellanlagringsdatabas.

Vid migreringen krävs ett PowerShell-skript som extraherar de befintliga inställningarna för användning i en ny installation.Använd den här metoden för att katalogisera inställningarna för den befintliga servern och tillämpa dem på en nyinstallerad mellanlagrings Server.Om du jämför inställningarna för den ursprungliga servern med en nyligen skapad Server kan du snabbt visualisera ändringarna mellan servrarna.Som alltid följer du organisationens certifierings process för att se till att ingen ytterligare konfiguration krävs.

### <a name="migration-process"></a>Migreringsprocessen 
Så här migrerar du inställningarna:

1. Starta **AzureADConnect.msi** på den nya mellanlagringsdatabasen och stoppa på **välkomst** sidan för Azure AD Connect.

1. Kopiera **MigrateSettings.ps1** från katalogen Microsoft Azure AD Connect\Tools till en plats på den befintliga servern. Ett exempel är C:\setup, där installations programmet är en katalog som skapades på den befintliga servern.

   ![Skärm bild som visar Azure AD Connect kataloger.](media/how-to-connect-import-export-config/migrate1.png)

1. Kör skriptet som det visas här och spara hela Server konfigurations katalogen på äldre nivå. Kopiera den här katalogen till den nya Staging-servern. Du måste kopiera hela den **exporterade-ServerConfiguration-*-** mappen till den nya servern.

   ![Skärm bild som visar skript i Windows PowerShell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Skärm bild som visar hur du kopierar den exporterade-ServerConfiguration-*-mappen.](media/how-to-connect-import-export-config/migrate3.png)

1. Starta **Azure AD Connect** genom att dubbelklicka på ikonen på Skriv bordet. Godkänn licens villkoren för program vara från Microsoft och välj sedan **Anpassa** på nästa sida.
1. Markera kryss rutan **Importera synkroniseringsinställningar** . Välj **Bläddra** för att bläddra i mappen kopierad – över exporterad-ServerConfiguration-*. Välj MigratedPolicy.jspå för att importera de migrerade inställningarna.

   ![Skärm bild som visar alternativet importera synkroniseringsinställningar.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Verifiering efter installation 

Att jämföra den ursprungliga importerade inställnings filen med den exporterade inställnings filen för den nyligen distribuerade servern är ett viktigt steg i att förstå skillnaderna mellan den avsedda och den resulterande distributionen. Genom att använda ditt favorit program sida-vid-sida-jämförelse ger du en direkt visualisering som snabbt visar alla önskade ändringar eller oavsiktliga ändringar.

Även om många tidigare manuella konfigurations steg nu har eliminerats bör du fortfarande följa organisationens certifierings process för att se till att ingen ytterligare konfiguration krävs. Den här konfigurationen kan inträffa om du använder avancerade inställningar, som för närvarande inte samlas in i den här versionen av inställnings hantering.

Här är kända begränsningar:
- **Regler för synkronisering**: prioriteten för en anpassad regel måste vara i det reserverade intervallet 0 till 99 för att undvika konflikter med Microsofts standard regler. Att placera en anpassad regel utanför det reserverade intervallet kan leda till att din anpassade regel flyttas runt när standard reglerna läggs till i konfigurationen. Ett liknande problem kan uppstå om konfigurationen innehåller ändrade standard regler. Att ändra en standard regel rekommenderas inte, och regel placeringen är förmodligen felaktig.
- **Tillbakaskrivning av enhet**: de här inställningarna är katalogiserade. De används för närvarande inte under konfigurationen. Om tillbakaskrivning av enheten har Aktiver ATS för den ursprungliga servern måste du konfigurera funktionen manuellt på den nya distribuerade servern.
- **Synkroniserade objekt typer**: även om det är möjligt att begränsa listan över synkroniserade objekt typer (t. ex. användare, kontakter och grupper) med hjälp av Synchronization Service Manager, stöds inte den här funktionen för tillfället via synkroniseringsinställningar. När du har slutfört installationen måste du manuellt tillämpa den avancerade konfigurationen.
- **Anpassade körnings profiler**: även om det är möjligt att ändra standard uppsättningen körnings profiler med hjälp av Synchronization Service Manager, stöds inte den här funktionen för närvarande via synkroniseringsinställningar. När du har slutfört installationen måste du manuellt tillämpa den avancerade konfigurationen.
- **Konfigurera etablerings hierarkin**: den här avancerade funktionen i Synchronization Service Manager stöds inte via synkroniseringsinställningar. Den måste konfigureras manuellt när du har slutfört den första distributionen.
- **Active Directory Federation Services (AD FS) (AD FS) och PingFederate-autentisering**: de inloggnings metoder som är kopplade till dessa funktioner förmarkeras automatiskt. Du måste ange alla andra nödvändiga konfigurations parametrar interaktivt.
- **En inaktive rad anpassad Synkroniseringsregel importeras som aktive rad**: en inaktive rad anpassad Synkroniseringsregel importeras som aktive rad. Se till att inaktivera den på den nya servern.

 ## <a name="next-steps"></a>Nästa steg

- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
