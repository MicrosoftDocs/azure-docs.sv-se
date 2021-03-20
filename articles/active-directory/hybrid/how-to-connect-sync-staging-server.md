---
title: 'Azure AD Connect synkronisering: operativa uppgifter och överväganden | Microsoft Docs'
description: I det här avsnittet beskrivs drift uppgifter för Azure AD Connect synkronisering och hur du förbereder för att driva den här komponenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090133"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: mellanlagrings Server och haveri beredskap
Med en server i mellanlagrings läge kan du göra ändringar i konfigurationen och förhandsgranska ändringarna innan du gör servern aktiv. Du kan också köra fullständig import och fullständig synkronisering för att kontrol lera att alla ändringar förväntas innan du gör dessa ändringar i produktions miljön.

## <a name="staging-mode"></a>Mellanlagringsläge
Mellanlagrings läge kan användas i flera scenarier, inklusive:

* Hög tillgänglighet.
* Testa och distribuera nya konfigurations ändringar.
* Presentera en ny server och inaktivera den gamla.

Under installationen kan du välja att servern ska vara i **mellanlagrings läge**. Den här åtgärden gör servern aktiv för import och synkronisering, men kör inte någon export. En server i mellanlagrings läge kör inte lösen ords synkronisering eller tillbakaskrivning av lösen ord, även om du har valt dessa funktioner under installationen. När du inaktiverar mellanlagrings läget, börjar servern exportera, aktiverar Lösenordssynkronisering och aktiverar tillbakaskrivning av lösen ord.

> [!NOTE]
> Anta att du har en aktive rad Azure AD Connect med funktionen för hash-synkronisering av lösen ord. När du aktiverar mellanlagrings läget slutar servern synkronisera lösen ords ändringar från den lokala AD-platsen. När du inaktiverar mellanlagrings läget, återupptar servern synkroniseringen av lösen ords ändringar från var den senast slutade. Om servern är kvar i mellanlagringsplatsen under en längre tid kan det ta en stund innan servern synkroniserar alla lösen ords ändringar som har inträffat under tids perioden.
>
>

Du kan fortfarande framtvinga en export genom att använda synkroniseringstjänsten för synkronisering.

En server i mellanlagringsplatsen fortsätter att ta emot ändringar från Active Directory och Azure AD och kan snabbt ta över ansvar för en annan server om det skulle uppstå ett fel. Om du gör konfigurations ändringar på den primära servern, är det ditt ansvar att göra samma ändringar på servern i mellanlagrings läge.

För dem som har kunskap om äldre Sync-tekniker är mellanlagringsplatsen annorlunda eftersom servern har en egen SQL-databas. Den här arkitekturen gör att mellanlagrings läges servern finns i ett annat data Center.

### <a name="verify-the-configuration-of-a-server"></a>Verifiera konfigurationen av en server
Följ dessa steg om du vill använda den här metoden:

1. [Förbereda](#prepare)
2. [Konfiguration](#configuration)
3. [Importera och synkronisera](#import-and-synchronize)
4. [Verifiera](#verify)
5. [Växla aktiv server](#switch-active-server)

#### <a name="prepare"></a>Förbereda
1. Installera Azure AD Connect, Välj **mellanlagrings läge** och avmarkera **Starta synkronisering** på den sista sidan i installations guiden. Med det här läget kan du köra Synkroniseringsmotorn manuellt.
   ![Skärm bild som visar sidan klar att konfigurera i dialog rutan Azure AD Connect.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Logga ut/logga in och välj **synkroniseringstjänst** i Start-menyn.

#### <a name="configuration"></a>Konfiguration
Om du har gjort anpassade ändringar på den primära servern och vill jämföra konfigurationen med mellanlagringsdatabasen använder du [Azure AD Connect konfigurations dokument](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importera och synkronisera
1. Välj **kopplingar** och välj den första anslutningen med typen **Active Directory Domain Services**. Klicka på **Kör**, Välj **fullständig import** och **OK**. Utför de här stegen för alla anslutningar av den här typen.
2. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **Kör**, Välj **fullständig import** och **OK**.
3. Se till att fliken kopplingar fortfarande är markerade. För varje koppling med typ **Active Directory Domain Services** klickar du på **Kör**, väljer **delta-synkronisering** och **OK**.
4. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **Kör**, Välj **delta-synkronisering** och **OK**.

Nu har du mellanlagrat export ändringar till Azure AD och lokala AD (om du använder Exchange hybrid-distribution). I nästa steg kan du kontrol lera vad som ska ändras innan du börjar exportera till katalogerna.

#### <a name="verify"></a>Verifiera
1. Starta en kommando tolk och gå till `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Kör: `csexport "Name of Connector" %temp%\export.xml /f:x` namnet på anslutningen kan hittas i synkroniseringstjänsten. Det har ett namn som liknar "contoso.com – Azure AD" för Azure AD.
3. Kör: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` du har en fil i% temp% med namnet export.csv som kan undersökas i Microsoft Excel. Den här filen innehåller alla ändringar som ska exporteras.
4. Gör nödvändiga ändringar i data eller konfiguration och kör de här stegen igen (importera och synkronisera och verifiera) tills ändringarna som ska exporteras förväntas.

**Förstå export.csv-filen** De flesta av filerna är själv för klar Ande. Några förkortningar för att förstå innehållet:
* OMODT – typ av objekt ändring. Anger om åtgärden på en objekt nivå är en Lägg till, uppdatera eller ta bort.
* AMODT – ändrings typ för attribut. Anger om åtgärden på en attribut-nivå är en Lägg till, uppdatera eller ta bort.

**Hämta vanliga identifierare** export.csv-filen innehåller alla ändringar som ska exporteras. Varje rad motsvarar en ändring av ett objekt i kopplings utrymmet och objektet identifieras av attributet DN. Attributet DN är en unik identifierare som tilldelats ett objekt i anslutnings utrymmet. När du har många rader/ändringar i export.csv att analysera, kan det vara svårt för dig att ta reda på vilka objekt som ändringarna endast gäller baserat på attributet DN. Använd csanalyzer.ps1 PowerShell-skriptet för att förenkla processen med att analysera ändringarna. Skriptet hämtar vanliga identifierare (till exempel displayName, userPrincipalName) för objekten. Använda skriptet:
1. Kopiera PowerShell-skriptet från avsnittet [CSAnalyzer](#appendix-csanalyzer) till en fil med namnet `csanalyzer.ps1` .
2. Öppna ett PowerShell-fönster och bläddra till den mapp där du skapade PowerShell-skriptet.
3. Kör: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nu har du en fil med namnet **processedusers1.csv** som kan undersökas i Microsoft Excel. Observera att filen innehåller en mappning från DN-attributet till vanliga identifierare (till exempel displayName och userPrincipalName). För närvarande ingår inte de faktiska attributändringar som ska exporteras.

#### <a name="switch-active-server"></a>Växla aktiv server
1. Stäng av servern (DirSync/FIM/Azure AD Sync) på den aktiva servern så att den inte exporteras till Azure AD eller Ställ in den i mellanlagrings läge (Azure AD Connect).
2. Kör installations guiden på servern i **mellanlagrings läge** och inaktivera **mellanlagrings läge**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Haveriberedskap
En del av implementerings utformningen är att planera för vad du ska göra om det uppstår en olycka där du förlorar Sync-servern. Det finns olika modeller som används och vilken som ska användas beror på flera faktorer, inklusive:

* Vad är din tolerans för att inte kunna göra ändringar i objekt i Azure AD under stillestånds tiden?
* Om du använder Lösenordssynkronisering måste användarna godkänna att de måste använda det gamla lösen ordet i Azure AD om de vill ändra den lokalt?
* Har du ett beroende på real tids åtgärder, till exempel tillbakaskrivning av lösen ord?

Beroende på svar på dessa frågor och din organisations policy kan en av följande strategier implementeras:

* Återskapa vid behov.
* Ha en reserv vänte läges server som kallas **mellanlagrings läge**.
* Använd virtuella datorer.

Om du inte använder den inbyggda SQL Express-databasen bör du även läsa avsnittet om [hög tillgänglighet för SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Återskapa vid behov
En praktisk strategi är att planera för att en server ska byggas om vid behov. Vanligt vis kan du installera Synkroniseringsmotorn och göra den ursprungliga importen och synkroniseringen inom några timmar. Om det inte finns någon tillgänglig reserv Server är det möjligt att tillfälligt använda en domänkontrollant som värd för Synkroniseringsmotorn.

Den synkroniserande motor servern lagrar inte något tillstånd om objekten så att databasen kan byggas om från data i Active Directory och Azure AD. Attributet **sourceAnchor** används för att ansluta objekten från lokala platser och molnet. Om du bygger om servern med befintliga objekt lokalt och i molnet, matchar Synkroniseringsmotorn dessa objekt tillsammans igen vid ominstallation. De saker du behöver för att dokumentera och spara är de konfigurations ändringar som görs på servern, till exempel filtrering och regler för synkronisering. De här anpassade konfigurationerna måste återanvändas innan du börjar synkronisera.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ha en förkonfigurerad standby-Server för spar läge
Om du har en mer komplex miljö rekommenderas att ha en eller flera vänte servrar. Under installationen kan du göra det möjligt för en server att bevaras i **mellanlagrings läge**.

Mer information finns i [mellanlagrings läge](#staging-mode).

### <a name="use-virtual-machines"></a>Använda virtuella datorer
En vanlig metod som stöds är att köra Synkroniseringsmotorn på en virtuell dator. Om värden har ett problem kan avbildningen med den synkroniserande motor servern migreras till en annan server.

### <a name="sql-high-availability"></a>Hög tillgänglighet för SQL
Om du inte använder SQL Server Express som medföljer Azure AD Connect bör även hög tillgänglighet för SQL Server beaktas. Lösningar för hög tillgänglighet som stöds är SQL-klustring och AOA (Always on-tillgänglighetsgrupper). Lösningar som inte stöds inkluderar spegling.

Stöd för SQL-AOA har lagts till i Azure AD Connect i version 1.1.524.0. Du måste aktivera SQL-AOA innan du installerar Azure AD Connect. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktive rad för SQL-AOA eller inte. Om SQL-AOA är aktive rad kan Azure AD Connect ytterligare siffror om SQL-AOA har kon figurer ATS för att använda synkron replikering eller asynkron replikering. När du konfigurerar tillgänglighets gruppens lyssnare rekommenderar vi att du ställer in egenskapen RegisterAllProvidersIP på 0. Detta beror på att Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client inte stöder användning av egenskapen MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Bilaga CSAnalyzer
Se avsnittet [kontrol lera](#verify) hur du använder det här skriptet.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**  

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)  
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)  
