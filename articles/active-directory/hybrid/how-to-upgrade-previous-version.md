---
title: 'Azure AD Connect: uppgradera från en tidigare version | Microsoft Docs'
description: Förklarar olika metoder för att uppgradera till den senaste versionen av Azure Active Directory Connect, inklusive en uppgradering på plats och en flyttning av sväng.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65fc0e84582c005c5796ceac86ee28fc46b2e1d8
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094224"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Uppgradera från en tidigare version till den senaste
I det här avsnittet beskrivs de olika metoder som du kan använda för att uppgradera din Azure Active Directory (Azure AD) Connect-installationen till den senaste versionen.  Du använder också stegen i avsnittet flytta [migrering](#swing-migration) när du gör en betydande konfigurations ändring.

>[!NOTE]
> Det är viktigt att du håller dina servrar uppdaterade med de senaste versionerna av Azure AD Connect. Vi gör hela tiden uppgraderingar av AADConnect, och dessa uppgraderingar innehåller korrigeringar av säkerhets problem och buggar, samt förbättringar av tjänster, prestanda och skalbarhet. För att se vad den senaste versionen är och för att lära dig vilka ändringar som har gjorts mellan olika versioner, se versions [historiken](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history) för version.

>[!NOTE]
> Det finns för närvarande stöd för att uppgradera från en version av Azure AD Connect till den aktuella versionen. Uppgraderingar på plats av DirSync eller ADSync stöds inte och en flyttning av sväng krävs.  Om du vill uppgradera från DirSync kan du läsa mer i [Uppgradera från Azure AD Sync Tool (DirSync)](how-to-dirsync-upgrade-get-started.md) eller i avsnittet flytta [migrering](#swing-migration) .  </br>I praktiken kan kunder i mycket äldre versioner drabbas av problem som inte är direkt relaterade till Azure AD Connect. Servrar som har varit i produktion under flera år, har vanligt vis haft flera korrigeringar som har tillämpats på dem och inte alla kan redovisas.  I allmänhet bör kunder som inte har uppgraderats i 12-18 månader överväga en sväng uppgradering i stället för det mest restriktivaste och minst riskfyllda alternativet.

Om du vill uppgradera från DirSync, se [Uppgradera från Azure AD Sync Tool (DirSync)](how-to-dirsync-upgrade-get-started.md) i stället.

Det finns några olika strategier som du kan använda för att uppgradera Azure AD Connect.

| Metod | Beskrivning |
| --- | --- |
| [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) |Detta är den enklaste metoden för kunder med en snabb installation. |
| [Uppgradering på plats](#in-place-upgrade) |Om du har en enda server kan du uppgradera installationen på plats på samma server. |
| [Swingmigrering](#swing-migration) |Med två servrar kan du förbereda en av servrarna med den nya versionen eller konfigurationen och ändra den aktiva servern när du är klar. |

Information om behörigheter finns i de [behörigheter som krävs för en uppgradering](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> När du har aktiverat den nya Azure AD Connect servern för att starta synkroniseringen av ändringar till Azure AD, kan du inte återställa till att använda DirSync eller Azure AD Sync. Nedgradering från Azure AD Connect till äldre klienter, inklusive DirSync och Azure AD Sync, stöds inte och kan leda till problem som data förlust i Azure AD.

## <a name="in-place-upgrade"></a>Uppgradering på plats
En uppgradering på plats fungerar för att flytta från Azure AD Sync eller Azure AD Connect. Det fungerar inte för att flytta från DirSync eller för en lösning med Forefront Identity Manager (FIM) + Azure AD-anslutning.

Den här metoden rekommenderas om du har en enda server och färre än cirka 100 000 objekt. Om det finns några ändringar i de färdiga Sync-reglerna sker en fullständig import och fullständig synkronisering efter uppgraderingen. Den här metoden säkerställer att den nya konfigurationen tillämpas på alla befintliga objekt i systemet. Den här körningen kan ta några timmar, beroende på antalet objekt som finns i omfånget för Sync-motorn. Den vanliga Schemaläggaren för synkronisering (som synkroniserar var 30: e minut som standard) är inaktive rad, men Lösenordssynkronisering fortsätter. Du kan överväga att utföra en uppgradering på plats under en helg. Om det inte finns några ändringar i den inbyggda konfigurationen med den nya Azure AD Connect versionen, startar en normal delta import/synkronisering i stället.  
![Uppgradering på plats](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Om du har gjort ändringar i de färdiga reglerna för synkronisering är de här reglerna tillbaka till standard konfigurationen vid uppgradering. Kontrol lera att konfigurationen hålls mellan uppgraderingar genom att se till att du gör ändringar som de beskrivs i [metod tips för att ändra standard konfigurationen](how-to-connect-sync-best-practices-changing-default-configuration.md).

Vid uppgradering på plats kan det finnas ändringar som kräver att vissa synkroniserings aktiviteter (inklusive steget fullständig import och fullständig synkronisering) körs när uppgraderingen har slutförts. Om du vill skjuta upp sådana aktiviteter läser du avsnittet [hur du skjuter upp fullständig synkronisering efter uppgraderingen](#how-to-defer-full-synchronization-after-upgrade).

Om du använder Azure AD Connect med en anslutning som inte är standard (till exempel allmän LDAP-anslutning och allmän SQL-anslutning) måste du uppdatera motsvarande anslutnings konfiguration i [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) efter uppgradering på plats. Mer information om hur du uppdaterar anslutnings konfigurationen finns i artikel avsnitt [Connector version versions historik-fel sökning](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Om du inte uppdaterar konfigurationen kommer import-och export körnings stegen inte att fungera korrekt för anslutnings tjänsten. Följande fel meddelande visas i program händelse loggen med meddelandet *"sammansättnings version i AAD Connector-konfigurationen (x. x. xxx. x) är tidigare än den faktiska versionen (" x. x. xxx. x ") för" C:\Program\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ".*

## <a name="swing-migration"></a>Swingmigrering
Om du har en komplex distribution eller många objekt kan det vara opraktiskt att göra en uppgradering på plats i Live-systemet. För vissa kunder kan den här processen ta flera dagar – och under den tiden bearbetas inga delta ändringar. Du kan också använda den här metoden när du planerar att göra betydande ändringar i konfigurationen och du vill testa dem innan de skickas till molnet.

Den rekommenderade metoden för de här scenarierna är att använda en sväng migrering. Du behöver (minst) två servrar--en aktiv server och en fristående server. Den aktiva servern (visas med heldragna blå linjer i följande bild) ansvarar för den aktiva produktions belastningen. Mellanlagrings servern (visas med streckade lila linjer) förbereds med den nya versionen eller konfigurationen. När den är helt klar görs den här servern aktiv. Den tidigare aktiva servern, som nu har den gamla versionen eller konfigurationen installerad, görs i den fristående servern och uppgraderas.

De två servrarna kan använda olika versioner. Till exempel kan den aktiva servern som du planerar att inaktivera använda Azure AD Sync och den nya mellanlagringsplatsen kan använda Azure AD Connect. Om du använder flytta migrering för att utveckla en ny konfiguration, är det en bra idé att ha samma versioner på de två servrarna.  
![Mellanlagrings Server](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Vissa kunder föredrar tre eller fyra servrar för det här scenariot. När du har uppgraderat en uppsamlings Server har du inte någon reserv Server för [haveri beredskap](how-to-connect-sync-staging-server.md#disaster-recovery). Med tre eller fyra servrar kan du förbereda en uppsättning primära/vänte servrar med den nya versionen, vilket garanterar att det alltid finns en fristående server som är redo att ta över.

De här stegen fungerar också för att flytta från Azure AD Sync eller en lösning med FIM + Azure AD-anslutning. De här stegen fungerar inte för DirSync, men samma sväng metod för flyttning (även kallat parallell distribution) med steg för DirSync är [uppgradering Azure Active Directory Sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Använd en flyttnings flyttning för att uppgradera
1. Om du använder Azure AD Connect på båda servrarna och endast planerar att göra en konfigurations ändring kontrollerar du att den aktiva servern och den fristående servern använder samma version. Det gör det enklare att jämföra skillnader senare. Om du uppgraderar från Azure AD Sync har dessa servrar olika versioner. Om du uppgraderar från en äldre version av Azure AD Connect, är det en bra idé att börja med de två servrar som använder samma version, men det är inte obligatoriskt.
2. Om du har gjort en anpassad konfiguration och din mellanlagrings Server inte har den, följer du stegen i [flytta en anpassad konfiguration från den aktiva servern till den fristående servern](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Om du uppgraderar från en tidigare version av Azure AD Connect uppgraderar du mellanlagrings servern till den senaste versionen. Om du flyttar från Azure AD Sync kan du installera Azure AD Connect på din mellanlagrings Server.
4. Låt Synkroniseringsmotorn köra fullständig import och fullständig synkronisering på din Staging Server.
5. Kontrol lera att den nya konfigurationen inte orsakade några oväntade ändringar genom att följa stegen under "verifiera" i [Verifiera konfigurationen av en server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Om något inte är som förväntat, korrigerar du det, kör import och synkronisering och kontrollerar data tills det ser bra ut genom att följa stegen.
6. Växla mellan mellanlagrings servern och den aktiva servern. Detta är det sista steget "växla aktiv server" i [Verifiera konfigurationen av en server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Om du uppgraderar Azure AD Connect uppgraderar du servern som nu är i mellanlagrings läge till den senaste versionen. Följ samma steg som innan du hämtar data och konfigurationen som har uppgraderats. Om du har uppgraderat från Azure AD Sync kan du nu stänga av och inaktivera den gamla servern.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Flytta en anpassad konfiguration från den aktiva servern till den fristående servern
Om du har gjort konfigurations ändringar på den aktiva servern måste du se till att samma ändringar tillämpas på mellanlagrings servern. För att hjälpa dig med den här flytten kan du använda [Azure AD Connect konfigurations dokument](https://github.com/Microsoft/AADConnectConfigDocumenter).

Du kan flytta reglerna för anpassad synkronisering som du har skapat med hjälp av PowerShell. Du måste använda andra ändringar på samma sätt på båda systemen och du kan inte migrera ändringarna. [Konfigurations Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter) kan hjälpa dig att jämföra de två systemen och se till att de är identiska. Verktyget kan också hjälpa dig att automatisera stegen i det här avsnittet.

Du måste konfigurera följande saker på samma sätt på båda servrarna:

* Anslutning till samma skogar
* Valfri domän och OU-filtrering
* Samma valfria funktioner, till exempel lösen ords synkronisering och tillbakaskrivning av lösen ord

**Flytta regler för anpassad synkronisering**  
För att flytta anpassade regler för synkronisering gör du följande:

1. Öppna **Redigeraren för regler för synkronisering** på din aktiva Server.
2. Välj en anpassad regel. Klicka på **Exportera**. Nu visas ett antecknings fönster. Spara den temporära filen med ett PS1-tillägg. Detta gör det till ett PowerShell-skript. Kopiera PS1-filen till mellanlagrings servern.  
   ![Export av Synkroniseringsregel](./media/how-to-upgrade-previous-version/exportrule.png)
3. GUID för kopplingen skiljer sig från den fristående servern och du måste ändra det. Hämta GUID genom att starta **Redigeraren för regler för synkronisering**, Välj en av de färdiga reglerna som representerar samma anslutna system och klicka på **Exportera**. Ersätt GUID i din PS1-fil med GUID från mellanlagrings servern.
4. Kör PS1-filen i en PowerShell-prompt. Detta skapar den anpassade synkroniseringsregeln på mellanlagrings servern.
5. Upprepa detta för alla anpassade regler.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Skjuta upp fullständig synkronisering efter uppgraderingen
Vid uppgradering på plats kan det finnas ändringar som kräver att vissa aktiviteter för synkronisering (inklusive steget fullständig import och fullständig synkronisering) körs. Till exempel Kräv att ändringar av kopplings scheman kräver **fullständigt import** steg och att ändringar i den inbyggda synkroniseringsregeln kräver att ett **fullständigt synkroniserings** steg körs på berörda anslutningar. Under uppgraderingen bestämmer Azure AD Connect vilka synkroniserings aktiviteter som krävs och registrerar dem som *åsidosättningar*. I följande synkroniseringsprocess hämtar synkroniseringsschemat dessa åsidosättningar och kör dem. När en åsidosättning har körts tas den bort.

Det kan finnas situationer där du inte vill att dessa åsidosättningar ska äga rum direkt efter uppgraderingen. Du kan till exempel ha flera synkroniserade objekt och du vill att de här stegen för synkronisering ska inträffa efter kontors tid. Ta bort dessa åsidosättningar:

1. Under uppgraderingen **avmarkerar** du alternativet **starta synkroniseringsprocessen när konfigurationen är klar**. Detta inaktiverar synkroniseringsschemat och förhindrar att synkroniseringen sker automatiskt innan åsidosättningarna tas bort.

   ![Skärm bild som visar att alternativet starta synkroniseringen När konfigurationen är klart måste avmarkeras.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. När uppgraderingen är klar kör du följande cmdlet för att ta reda på vilka åsidosättningar som har lagts till: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Åsidosättningarna är anslutningsspecifika. I följande exempel har steget fullständig import och fullständig synkronisering lagts till både i den lokala AD-anslutningen och Azure AD-anslutaren.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Anteckna de befintliga åsidosättningarna som har lagts till.
   
4. Om du vill ta bort åsidosättningarna för både fullständig import och fullständig synkronisering på en godtycklig koppling kör du följande cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Om du vill ta bort åsidosättningarna för alla anslutningar kör du följande PowerShell-skript:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Kör följande cmdlet för att återuppta Schemaläggaren: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Kom ihåg att köra de nödvändiga stegen för synkronisering vid din tidigaste bekvämlighet. Du kan antingen utföra dessa steg manuellt med hjälp av Synchronization Service Manager eller lägga till åsidosättningarna igen med hjälp av Set-ADSyncSchedulerConnectorOverride-cmdleten.

Om du vill lägga till åsidosättningarna för både fullständig import och fullständig synkronisering på en godtycklig koppling kör du följande cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Felsökning
Följande avsnitt innehåller fel sökning och information som du kan använda om du stöter på problem med att uppgradera Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure Active Directory anslutnings fel som saknas vid Azure AD Connect uppgradering

När du uppgraderar Azure AD Connect från en tidigare version kan du trycka på följande fel i början av uppgraderingen 

![Fel](./media/how-to-upgrade-previous-version/error1.png)

Det här felet beror på att Azure Active Directory-anslutningen med identifierare, b891884f-051e-4a83-95af-2544101c9083, inte finns i den aktuella Azure AD Connect konfigurationen. Du kan kontrol lera detta genom att öppna ett PowerShell-fönster, köra cmdleten `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

PowerShell-cmdleten rapporterar felet **Det gick inte att hitta det angivna ma**.

Orsaken till detta är att den aktuella Azure AD Connect-konfigurationen inte stöds för uppgradering. 

Om du vill installera en nyare version av Azure AD Connect: Stäng Azure AD Connect-guiden, avinstallera den befintliga Azure AD Connect och utför en ren installation av den nyare Azure AD Connect.



## <a name="next-steps"></a>Nästa steg
Läs mer om hur [du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
