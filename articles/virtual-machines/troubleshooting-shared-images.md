---
title: Felsöka problem med delade avbildningar i Azure
description: Lär dig hur du felsöker problem med delade bild gallerier.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: d80caf767d923ce2539ca254a8312371155a3104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553739"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Felsöka delade avbildnings gallerier i Azure

Om du har problem med att utföra åtgärder på delade avbildnings gallerier, bild definitioner och avbildnings versioner kör du kommandot som inte kan köras igen i fel söknings läge. Du aktiverar fel söknings läge genom `--debug` att skicka växeln med Azure CLI och `-Debug` växeln med PowerShell. När du har hittat felet följer du den här artikeln för att felsöka den.


## <a name="creating-or-modifying-a-gallery"></a>Skapa eller ändra ett galleri ##

**Meddelande**: *Galleri namnet är ogiltigt. Tillåtna tecken är engelska alfanumeriska tecken, med under streck och punkter tillåtna i mitten, upp till 80 tecken totalt. Alla övriga specialtecken, inklusive streck, är inte tillåtna.*  
**Orsak**: namnet på galleriet uppfyller inte namngivnings kraven.  
**Lösning**: Välj ett namn som uppfyller följande villkor: 
- Har en gräns på 80 tecken
- Innehåller bara engelska bokstäver, siffror, under streck och punkter
- Startar och slutar med engelska bokstäver eller siffror

**Meddelande**: *entitetsnamnet galleryName är ogiltigt enligt validerings regeln: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Orsak**: Galleri namnet uppfyller inte namngivnings kraven.  
**Lösning**: Välj ett namn för galleriet som uppfyller följande villkor: 
- Har en gräns på 80 tecken
- Innehåller bara engelska bokstäver, siffror, under streck och punkter
- Startar och slutar med engelska bokstäver eller siffror

**Meddelande**: *det tillhandahållna resurs namnet <galleryName \> innehåller följande ogiltiga efterföljande tecken: <tecken \> . Namnet får inte sluta med tecken: <tecken \>*  
**Orsak**: namnet på galleriet slutar med en punkt eller ett under streck.  
**Lösning**: Välj ett namn för galleriet som uppfyller följande villkor: 
- Har en gräns på 80 tecken
- Innehåller bara engelska bokstäver, siffror, under streck och punkter
- Startar och slutar med engelska bokstäver eller siffror

**Meddelande**: *den angivna platsen <region \> är inte tillgänglig för resurs typen Microsoft. Compute/Galleries. Lista över tillgängliga regioner för resurs typen är...*  
**Orsak**: den region som har angetts för galleriet är felaktig eller kräver en åtkomstbegäran.  
**Lösning**: kontrol lera att regions namnet är korrekt. Om region namnet är korrekt skickar du [en](/troubleshoot/azure/general/region-access-request-process) åtkomstbegäran för regionen.

**Meddelande**: det *går inte att ta bort resursen innan kapslade resurser tas bort.*  
**Orsak**: du har försökt att ta bort ett galleri som innehåller minst en befintlig avbildnings definition. Ett galleri måste vara tomt innan det kan tas bort.  
**Lösning**: ta bort alla bild definitioner i galleriet och fortsätt sedan att ta bort galleriet. Om avbildnings definitionen innehåller bild versioner måste du ta bort avbildnings versionerna innan du tar bort avbildnings definitionerna.

**Meddelande**: *Galleri namnet <galleryName \> är inte unikt i prenumerationen <subscriptionId> . Välj ett annat namn på galleriet.*  
**Orsak**: du har ett befintligt galleri med samma namn och har försökt att skapa ett annat galleri med samma namn.  
**Lösning**: Välj ett annat namn för galleriet.

**Meddelande**: *resurs <s galleryName \> finns redan på platsen <region \_ 1 \> i resurs gruppen <resourceGroup \> . Det går inte att skapa en resurs med samma namn på platsen <region \_ 2 \> . Välj ett nytt resurs namn.*  
**Orsak**: du har ett befintligt galleri med samma namn och har försökt att skapa ett annat galleri med samma namn.  
**Lösning**: Välj ett annat namn för galleriet.

## <a name="creating-or-modifying-image-definitions"></a>Skapa eller ändra bild definitioner ##

**Meddelande**: *\> det går inte att ändra egenskapen galleryImage. Properties. <Property.*  
**Orsak**: du försökte ändra OS-typ, operativ system tillstånd, Hyper-V-generering, erbjudande, utgivare eller SKU. Det är inte tillåtet att ändra någon av dessa egenskaper.  
**Lösning**: skapa en ny avbildnings definition i stället.

**Meddelande**: *resurs <galleryName/imageDefinitionName \> finns redan på platsen <region \_ 1 \> i resurs gruppen <resourceGroup \> . Det går inte att skapa en resurs med samma namn på platsen <region \_ 2 \> . Välj ett nytt resurs namn.*  
**Orsak**: du har en befintlig avbildnings definition i samma galleri och resurs grupp med samma namn. Du har försökt att skapa en annan avbildnings definition med samma namn och i samma Galleri, men i en annan region.  
**Lösning**: Använd ett annat namn för avbildnings definitionen eller Lägg till bild definitionen i ett annat galleri eller en annan resurs grupp.

**Meddelande**: *det tillhandahållna resurs namnet <GalleryName \> /<imageDefinitionName \> har följande ogiltiga efterföljande tecken: <tecken \> . Namnet får inte sluta med tecken: <tecken \>*  
**Orsak**: <imageDefinitionName \> namn slutar med en punkt eller ett under streck.  
**Lösning**: Välj ett namn för avbildnings definitionen som uppfyller följande villkor: 
- Har en gräns på 80 tecken
- Innehåller bara engelska bokstäver, siffror, under streck, bindestreck och punkter
- Startar och slutar med engelska bokstäver eller siffror.

**Meddelande**: *entitetsnamnet <imageDefinitionName \> är ogiltigt enligt validerings regeln: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Orsak**: <imageDefinitionName \> namn slutar med en punkt eller ett under streck.  
**Lösning**: Välj ett namn för avbildnings definitionen som uppfyller följande villkor: 
- Har en gräns på 80 tecken
- Innehåller bara engelska bokstäver, siffror, under streck, bindestreck och punkter
- Startar och slutar med engelska bokstäver eller siffror

**Meddelande**: *till gångs namn galleryImage. Properties. ID. <egenskapen \> är ogiltig. Det får inte vara tomt. Tillåtna tecken är versaler eller gemener, siffror, bindestreck (-), punkt (.), under streck ( \_ ). Namn får inte avslutas med punkt (.). Längden på namnet får inte överskrida <siffer \> tecken.*  
**Orsak**: utgivar-, erbjudande-eller SKU-värdet uppfyller inte namngivnings kraven.  
**Lösning**: Välj ett värde som uppfyller följande villkor: 
- Har en gräns på 128 tecken för utgivare eller 64 tecken gräns för erbjudande och SKU
- Innehåller bara engelska bokstäver, siffror, bindestreck, under streck och punkter
- Slutar inte med en punkt

**Meddelande**: *det går inte att utföra den begärda åtgärden på en kapslad resurs. Det \> gick inte att hitta den överordnade resurs <galleryName.*  
**Orsak**: det finns inget galleri med namnet <galleryName \> i den aktuella prenumerationen och resurs gruppen.  
**Lösning**: kontrol lera att namnen på galleriet, prenumerationen och resurs gruppen är korrekta. Annars skapar du ett nytt galleri med namnet <galleryName \> .

**Meddelande**: *den angivna platsen <region \> är inte tillgänglig för resurs typen Microsoft. Compute/Galleries. Lista över tillgängliga regioner för resurs typen är...*  
**Orsak**: namnet på den <regionen \> är felaktigt eller kräver en åtkomstbegäran.  
**Lösning**: kontrol lera att region namnet är rättstavat. Du kan köra det här kommandot för att se vilka regioner du har åtkomst till. Om regionen inte finns med i listan skickar du [en åtkomstbegäran](/troubleshoot/azure/general/region-access-request-process).

**Meddelande**: *det går inte att serialisera värde: <värde \> som typ: ISO-8601., ISO8601Error: ISO 8601 Time beskrivare saknas. Det gick inte att parsa datetime \> -strängens <värde*  
**Orsak**: det värde som har angetts för egenskapen är inte korrekt formaterat som ett datum.  
**Lösning**: Ange ett datum i formatet åååå-mm-dd, åååå-mm-dd'T'HH: mm: Sszzz eller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-giltigt.

**Meddelande**: *Det gick inte att konvertera strängen till DateTimeOffset: <värde \> . Sök vägs egenskaper. <egenskapen \>*  
**Orsak**: det värde som har angetts för egenskapen är inte korrekt formaterat som ett datum.  
**Lösning**: Ange ett datum i formatet åååå-mm-dd, åååå-mm-dd'T'HH: mm: Sszzz eller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-giltigt.

**Meddelande**: *EndOfLifeDate måste anges till ett framtida datum.*  
**Orsak**: slut på livs längds egenskapen är inte korrekt formaterad som ett datum som infaller efter dagens datum.  
**Lösning**: Ange ett datum i formatet åååå-mm-dd, åååå-mm-dd'T'HH: mm: Sszzz eller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-giltigt.

**Meddelande**: *argument--<egenskap \> : ogiltigt heltals värde: <\> värde*  
**Orsak**: egenskapen <\> accepterar endast heltals värden och <värdet \> är inte ett heltal.  
**Lösning**: Välj ett heltals värde.

**Meddelande**: *det minimala värdet för <-egenskapen \> får inte vara större än det maximala värdet för <egenskap \> .*  
**Orsak**: det minsta värde som har angetts för <egenskap \> är högre än det högsta värdet som angetts för egenskapen <\> .  
**Lösning**: ändra värdena så att minimivärdet är mindre än eller lika med max värdet.

**Meddelande**: *galleri bild: <imageDefinitionName \> som identifieras av (utgivare: <utgivare \> , erbjudande: <erbjudande \> , SKU: <SKU \> ) finns redan. Välj en annan utgivare, erbjudande, SKU-kombination.*  
**Orsak**: du har försökt skapa en ny avbildnings definition med samma utgivare, erbjudande och SKU-trippel som en befintlig avbildnings definition i samma Galleri.  
**Lösning**: i ett galleri måste alla bild definitioner ha en unik kombination av utgivare, erbjudande och SKU. Välj en unik kombination eller Välj ett nytt galleri och skapa bild definitionen igen.

**Meddelande**: det *går inte att ta bort resursen innan kapslade resurser tas bort.*  
**Orsak**: du har försökt att ta bort en avbildnings definition som innehåller bild versioner. En avbildnings definition måste vara tom innan den kan tas bort.  
**Lösning**: ta bort alla avbildnings versioner inuti avbildnings definitionen och fortsätt sedan att ta bort avbildnings definitionen.

**Meddelande**: *det går inte att binda parameter <egenskap \> . Det går inte att konvertera värdet <värde \> till typen <propertyType \> . Det går inte att matcha ID-namnet <värdet \> till ett giltigt uppräknings namn. Ange ett av följande namn uppräknings namn och försök igen: <choice1 \> <choice2 \> ...*  
**Orsak**: egenskapen innehåller en begränsad lista med möjliga värden och <värdet \> är inte en av dem.  
**Lösning**: Välj ett av de möjliga <Choice- \> värdena.

**Meddelande**: *det går inte att binda parameter <egenskap \> . Det går inte att konvertera värdet <värde \> till Type &quot; system. DateTime &quot;*  
**Orsak**: det värde som har angetts för egenskapen är inte korrekt formaterat som ett datum.  
**Lösning**: Ange ett datum i formatet åååå-mm-dd, åååå-mm-dd'T'HH: mm: Sszzz eller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-giltigt.

**Meddelande**: *det går inte att binda parameter <egenskap \> . Det går inte att konvertera värdet <värde \> till typen &quot; system. Int32 &quot;*  
**Orsak**: egenskapen <\> accepterar endast heltals värden och <värdet \> är inte ett heltal.  
**Lösning**: Välj ett heltals värde.

**Meddelande**: *ZRS-lagrings konto typen stöds inte i den här regionen.*  
**Orsak**: du har valt standard Zone-redundant lagring (ZRS) i en region som ännu inte har stöd för det.  
**Lösning**: ändra lagrings konto typen till **Premium- \_ LRS** eller **standard- \_ LRS**. Läs vår dokumentation för den senaste [listan med regioner](../storage/common/storage-redundancy.md#zone-redundant-storage) med ZRS Preview aktive rad.

## <a name="creating-or-updating-image-versions"></a>Skapa eller uppdatera avbildnings versioner ##

**Meddelande**: *den angivna platsen <region \> är inte tillgänglig för resurs typen Microsoft. Compute/Galleries. Lista över tillgängliga regioner för resurs typen är...*  
**Orsak**: namnet på den <regionen \> är felaktigt eller kräver en åtkomstbegäran.  
**Lösning**: kontrol lera att region namnet är rättstavat. Du kan köra det här kommandot för att se vilka regioner du har åtkomst till. Om regionen inte finns med i listan skickar du [en åtkomstbegäran](/troubleshoot/azure/general/region-access-request-process).

**Meddelande**: *det går inte att utföra den begärda åtgärden på en kapslad resurs. Det \> gick inte att hitta överordnad resurs <galleryName/imageDefinitionName.*  
**Orsak**: det finns inget galleri med namnet <GalleryName/imageDefinitionName \> i den aktuella prenumerationen och resurs gruppen.  
**Lösning**: kontrol lera att namnen på galleriet, prenumerationen och resurs gruppen är korrekta. Annars skapar du ett nytt galleri med namnet <galleryName \> och/eller en bild definition med namnet <imageDefinitionName \> i den angivna resurs gruppen.

**Meddelande**: *det går inte att binda parameter <egenskap \> . Det går inte att konvertera värdet <värde \> till Type &quot; system. DateTime &quot;*  
**Orsak**: det värde som har angetts för egenskapen är inte korrekt formaterat som ett datum.  
**Lösning**: Ange ett datum i formatet åååå-mm-dd, åååå-mm-dd'T'HH: mm: Sszzz eller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-giltigt.

**Meddelande**: *det går inte att binda parameter <egenskap \> . Det går inte att konvertera värdet <värde \> till typen &quot; system. Int32 &quot;*  
**Orsak**: egenskapen <\> accepterar endast heltals värden och <värdet \> är inte ett heltal.  
**Lösning**: Välj ett heltals värde.

**Meddelande**: *Galleri avbildnings version publicerings profil regioner <publishingRegions \> måste innehålla bild versions platsen <\> sourceRegion*  
**Orsak**: käll avbildningens plats (<sourceRegion \> ) måste ingå i <publishingRegions- \> listan.  
**Lösning**: inkludera <sourceRegion \> i listan <publishingRegions \> .

**Meddelande**: *värdet <värdet \> för parameter <-egenskapen \> ligger utanför intervallet. Värdet måste vara mellan <minValue \> och <maxValue \> .*  
**Orsak**: <värde \> ligger utanför intervallet av möjliga värden för egenskapen <\> .  
**Lösning**: Välj ett värde som ligger inom intervallet för <minValue \> och <maxValue \> , inklusive.

**Meddelande**: *\> Det gick inte att hitta käll <resourceID. Kontrol lera att källan finns och att den är i samma region som den Galleri avbildnings version som skapas.*  
**Orsak**: det finns ingen källa på <ResourceID \> eller också är källan på <ResourceID \> inte i samma region som Galleri avbildningen som skapas.  
**Lösning**: kontrol lera att värdet för <resourceID \> är rätt och att käll området för Galleri avbildnings versionen är detsamma som regionen för värdet för <resourceID \> .

**Meddelande**: *\> det är inte tillåtet att ändra egenskapen GalleryImageVersion. properties. storageProfile. <Diskimage om. Source.ID* .  
**Orsak**: det går inte att ändra käll-ID: t för en Galleri avbildnings version efter skapandet.  
**Lösning**: kontrol lera att käll-ID: t är samma som det befintliga käll-ID: t, ändra versions numret för avbildnings versionen eller ta bort den aktuella avbildnings versionen och försök igen.

**Meddelande**: *dubblerade LUN-nummer har identifierats i indata-diskarna. LUN-numret måste vara unikt för varje data disk.*  
**Orsak**: när du skapar en avbildnings version med hjälp av en lista över diskar och/eller disk ögonblicks bilder, har två eller flera diskar eller disk ögonblicks bilder samma LUN.  
**Lösning**: ta bort eller ändra dubbletter av LUN.

**Meddelande**: *dubbletter av käll-ID: n hittades i de angivna diskarna. Käll-ID måste vara unikt för varje disk.*  
**Orsak**: när du skapar en avbildnings version med hjälp av en lista över diskar och/eller disk ögonblicks bilder, har två eller flera diskar eller disk ögonblicks bilder samma resurs-ID.  
**Lösning**: ta bort eller ändra dubbletter av disk käll-ID: n.

**Meddelande**: *egenskaps-ID <resourceID \> vid sökväg ' properties. storageProfile. <diskImages \> . Source.ID ' är ogiltigt. Ett fullständigt kvalificerat resurs-ID som börjar med "/subscriptions/{subscriptionId}" eller "/providers/{resourceProviderNamespace}/" förväntas.*  
**Orsak**: <resourceID- \> värdet är felaktigt formaterat.  
**Lösning**: kontrol lera att resurs-ID: t är korrekt.

**Meddelande**: *käll-ID: <resourceID \> måste antingen vara en hanterad avbildning, en virtuell dator eller en annan Galleri avbildnings version*  
**Orsak**: <resourceID- \> värdet är felaktigt formaterat.  
**Lösning**: om du använder en virtuell dator, hanterad avbildning eller Galleri avbildnings version som käll avbildning, kontrollerar du att resurs-ID: t för den virtuella datorn, den hanterade avbildningen eller Galleri avbildnings versionen är korrekt.

**Meddelande**: *käll-ID: <resourceID \> måste vara en hanterad disk eller ögonblicks bild.*  
**Orsak**: <resourceID- \> värdet är felaktigt formaterat.  
**Lösning**: om du använder diskar och/eller disk ögonblicks bilder som källor för avbildnings versionen kontrollerar du att resurs-ID: n för diskarna och disk ögonblicks bilderna är korrekta.

**Meddelande**: *det går inte att skapa Galleri avbildnings Version från: <resourceID \> eftersom operativ systemets tillstånd i den överordnade galleri avbildningen (<OsState \_ 1 \> ) inte <OsState \_ 2 \> .*  
**Orsak**: operativ systemets tillstånd (generaliserad eller specialiserad) matchar inte det operativ system tillstånd som anges i avbildnings definitionen.  
**Lösning**: Välj antingen en källa som baseras på en virtuell dator med operativ systemets tillstånd för <OsState \_ 1 \> eller skapa en ny avbildnings definition för virtuella datorer baserat på <OsState \_ 2 \> .

**Meddelande**: *resursen med ID <resourceID \> har en annan hypervisor-generation [' <V # \_ 1 \> '] än den överordnade Galleri avbildningen hypervisor generation [' <V # \_ 2 \> ']*  
**Orsak**: hypervisor-genereringen av avbildnings versionen överensstämmer inte med den hypervisor-generation som anges i bild definitionen. Operativ systemet för avbildnings definition är <V # \_ 1 \> och avbildnings versionens operativ system är <V # \_ 2 \> .  
**Lösning**: Välj antingen en källa med samma hypervisor-generation som bild definitionen eller skapa/Välj en ny avbildnings definition som har samma hypervisor-generation som avbildnings versionen.

**Meddelande**: *resursen med ID <resourceID \> har en annan OS-typ [' <OsType \_ 1 \> '] än den överordnade Galleri avbildningen av operativ system typen [' <OsType \_ 2 \> ']*  
**Orsak**: hypervisor-genereringen av avbildnings versionen överensstämmer inte med den hypervisor-generation som anges i bild definitionen. Operativ systemet för avbildnings definition är <OsType \_ 1 \> och operativ systemet avbildnings version är <OsType \_ 2 \> .  
**Lösning**: Välj antingen en källa med samma operativ system (Linux/Windows) som bild definition eller skapa/Välj en ny avbildnings definition som har samma operativ Systems generation som avbildnings versionen.

**Meddelande**: *den virtuella käll datorn <resourceID \> kan inte innehålla en tillfällig OS-disk.*  
**Orsak**: källan på <resourceID \> innehåller en tillfällig OS-disk. Det finns för närvarande inte stöd för tillfälliga OS-diskar i det delade avbildnings galleriet.  
**Lösning**: Välj en annan källa baserat på en virtuell dator som inte använder en tillfällig OS-disk.

**Meddelande**: *den virtuella käll datorn <resourceID \> får inte innehålla disken [<diskID \> ] som lagras i en UltraSSD-kontotyp.*  
**Orsak**: disk <diskID \> är en ultra SSD disk. Det finns för närvarande inte stöd för Ultra SSD diskar i den delade avbildnings galleriet.  
**Lösning**: Använd en källa som bara innehåller Premium SSD, standard SSD och/eller standard HDD Managed disks.

**Meddelande**: *den virtuella käll datorn <resourceID \> måste skapas från Managed disks.*  
**Orsak**: den virtuella datorn i <resourceID \> använder ohanterade diskar.  
**Lösning**: Använd en källa som baseras på en virtuell dator som bara innehåller Premium SSD, standard SSD och/eller standard HDD Managed disks.

**Meddelande**: *för många begär anden på källan <resourceID \> . Minska antalet begär Anden på källan eller vänta en stund innan du försöker igen.*  
**Orsak**: källan för den här avbildnings versionen begränsas för tillfället på grund av för många begär Anden.  
**Lösning**: försök att skapa avbildnings versionen senare.

**Meddelande**: *disk krypterings uppsättningen <diskEncryptionSetID \> måste finnas i samma prenumeration <subscriptionID \> som Galleri resursen.*  
**Orsak**: disk krypterings uppsättningar kan bara användas i samma prenumeration och region som de skapades i.  
**Lösning**: skapa eller Använd en krypterings uppsättning i samma prenumeration och region som avbildnings versionen.

**Meddelande**: *krypterad Källa: <resourceID \> har ett annat prenumerations-ID än den aktuella Galleri avbildningens versions prenumeration <subscriptionID \_ 1 \> . Försök igen med en okrypterad källa eller Använd källans prenumeration <subcriptionID \_ 2 \> om du vill skapa en Galleri avbildnings version.*  
**Orsak**: det delade avbildnings galleriet stöder för närvarande inte skapande av avbildnings versioner i en annan prenumeration från en annan käll avbildning om käll avbildningen är krypterad.  
**Lösning**: Använd en okrypterad källa eller skapa avbildnings versionen i samma prenumeration som källan.

**Meddelande**: *det \> gick inte att hitta disk krypterings uppsättningen <diskEncryptionSetID.*  
**Orsak**: disk krypteringen kan vara felaktig.  
**Lösning**: kontrol lera att resurs-ID: t för disk krypterings uppsättningen är korrekt.

**Meddelande**: *avbildningens versions namn är ogiltigt. Avbildningens versions namn bör följa större (int). Moll (int). Korrigerings format (int), t. ex.: 1.0.0, 2018.12.1 osv.*  
**Orsak**: det giltiga formatet för en avbildnings version är tre heltal avgränsade med en punkt. Avbildningens versions namn uppfyllde inte det giltiga formatet.  
**Lösning**: Använd ett avbildnings versions namn som följer formatet Major (int). Moll (int). Korrigering (int). Till exempel: 1.0.0. eller 2018.12.1.

**Meddelande**: *värdet för parametern galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. dataDiskImages. diskEncryptionSetId är ogiltigt*  
**Orsak**: resurs-ID för den disk krypterings uppsättning som används på en data disk avbildning använder ett ogiltigt format.  
**Lösning**: kontrol lera att resurs-ID: t för disk krypterings uppsättningen följer formatet/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Meddelande**: *värdet för parametern galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId är ogiltigt.*  
**Orsak**: resurs-ID: t för den disk krypterings uppsättning som används på operativ systemets disk avbildning använder ett ogiltigt format.  
**Lösning**: kontrol lera att resurs-ID: t för disk krypterings uppsättningen följer formatet/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Meddelande**: *det går inte att ange en ny data disk avbildning kryptering LUN [<Number \> ] med en disk krypterings uppsättning i regionen [<region \> ] för uppdaterings galleriets avbildnings versions förfrågan. Ta bort den nya LUN-versionen för att uppdatera den här versionen. Om du behöver ändra krypterings inställningarna för data disk avbildningen måste du skapa en ny Galleri avbildnings version med rätt inställningar.*  
**Orsak**: du har lagt till kryptering till data disken för en befintlig avbildnings version. Du kan inte lägga till kryptering i en befintlig avbildnings version.  
**Lösning**: skapa en ny Galleri avbildnings version eller ta bort de tillagda krypterings inställningarna.

**Meddelande**: *galleriets artefakt versions källa kan bara anges antingen direkt under storageProfile eller inom enskilda OS eller data diskar. En och endast en källtyp (användar avbildning, ögonblicks bild, disk, virtuell dator) kan tillhandahållas.*  
**Orsak**: käll-ID saknas.  
**Lösning**: kontrol lera att källans käll-ID finns.

**Meddelande**: *Det gick inte att hitta källan: <resourceID \> . Kontrol lera att källan finns.*  
**Orsak**: resurs-ID: t för källan kan vara felaktigt.  
**Lösning**: kontrol lera att källans resurs-ID är korrekt.

**Meddelande**: *en disk krypterings uppsättning krävs för disken "galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId" i mål regionen "<region \_ 1 \> " eftersom disk krypterings uppsättningen <diskEncryptionSetId \> används för motsvarande disk i region "<region \_ 2 \> "*  
**Orsak**: kryptering har använts på OS-disken i <region \_ 2 \> , men inte i <region \_ 1 \> .  
**Lösning**: om du använder kryptering på OS-disken använder du kryptering i alla regioner.

**Meddelande**: *en disk krypterings uppsättning krävs för disken LUN <nummer \> i mål regionen <region \_ 1 \> eftersom disk krypterings uppsättningen <diskEncryptionSetID \> används för motsvarande disk i region <region \_ 2 \>*  
**Orsak**: krypteringen har använts på data disken på LUN <antalet \> i <region \_ 2 \> , men inte i <region \_ 1 \> .  
**Lösning**: om du använder kryptering på en data disk använder du kryptering i alla regioner.

**Meddelande**: *ett ogiltigt LUN [<Number \> ] angavs i kryptering. dataDiskImages. LUN-värdet måste vara något av följande värden: [0, 9].*  
**Orsak**: den LUN som angetts för krypteringen matchar inte någon av LUN för diskar som är anslutna till den virtuella datorn.  
**Lösning**: ändra LUN i krypteringen till LUN för en data disk som finns på den virtuella datorn.

**Meddelande**: *duplicerade LUN ' <Number \> ' angavs i mål regionen <region \> ' Encryption. dataDiskImages.*  
**Orsak**: de krypterings inställningar som används i <region \> angav en LUN minst två gånger.  
**Lösning**: ändra lun i <region \> för att se till att alla LUN är unika i <region \> .

**Meddelande**: *OSDiskImage och DataDiskImage kan inte peka på samma BLOB <\> SourceId*  
**Orsak**: källorna för operativ system disken och minst en datadisk är inte unika.  
**Lösning**: ändra källan för OS-disken och/eller data diskarna för att säkerställa att operativ systemets disk och varje data disk är unikt.

**Meddelande**: *dubbla regioner tillåts inte i mål publicerings regioner.*  
**Orsak**: en region listas bland publicerings regionerna mer än en gång.  
**Lösning**: ta bort den dubblerade regionen.

**Meddelande**: det *går inte att lägga till nya data diskar eller ändra LUN för en datadisk i en befintlig avbildning.*  
**Orsak**: ett uppdaterings anrop till avbildnings versionen innehåller antingen en ny datadisk eller en ny LUN för en disk.  
**Lösning**: Använd LUN och data diskar i den befintliga avbildnings versionen.

**Meddelande**: *disk krypterings uppsättningen <diskEncryptionSetID \> måste finnas i samma prenumeration <subscriptionID \> som Galleri resursen.*  
**Orsak**: det delade avbildnings galleriet stöder för närvarande inte användning av en disk krypterings uppsättning i en annan prenumeration.  
**Lösning**: skapa avbildnings versionen och disk krypterings uppsättningen i samma prenumeration.

**Meddelande**: *replikeringen misslyckades i den här regionen på grund av att GalleryImageVersion käll resurs storlek 2048 överskrider max storleken som stöds av 1024.*  
**Orsak**: en datadisk i källan är större än 1 TB.  
**Lösning**: ändra storlek på data disken till under 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Skapa eller uppdatera en virtuell dator eller skalnings uppsättningar från en avbildnings version ##

**Meddelande**: *det finns ingen senaste avbildnings version för "<imageDefinitionResourceID \> "*  
**Orsak**: avbildnings definitionen som du använde för att distribuera den virtuella datorn innehåller inte några avbildnings versioner som ingår i senaste.  
**Lösning**: kontrol lera att det finns minst en avbildnings version som har "exkludera från den senaste" inställt på falskt. 

**Meddelande**: *klienten har behörighet att utföra åtgärden Microsoft. Compute/Galleri/images/versions/read på omfattning <resourceID \> , men den aktuella klient <tenantId1 \> har inte behörighet att komma åt den länkade prenumerationen <subscriptionId2 \> .*  
**Orsak**: den virtuella datorn eller skalnings uppsättningen skapades via en sig-avbildning i en annan klient. Du har försökt göra en ändring i den virtuella datorn eller skalnings uppsättningen, men du har inte åtkomst till den prenumeration som äger avbildningen.  
**Lösning**: kontakta ägaren till prenumerationen på avbildnings versionen för att ge Läs behörighet till avbildnings versionen.

**Meddelande**: *galleri avbildningen <resourceID \> är inte tillgänglig i regionen <region \> . Kontakta avbildningens ägare för att replikera till den här regionen eller ändra din begärda region.*  
**Orsak**: den virtuella datorn skapas i en region som inte finns i listan över publicerade regioner för Galleri avbildningen.  
**Lösning**: du kan antingen replikera avbildningen till regionen eller skapa en virtuell dator i en av regionerna i Galleri avbildningens publicerings regioner.

**Meddelande**: *parametern "osProfile" är inte tillåten.*  
**Orsak**: administratörens användar namn, lösen ord eller SSH-nycklar angavs för en virtuell dator som har skapats från en specialiserad avbildnings version.  
**Lösning**: ta inte med administratörens användar namn, lösen ord eller SSH-nycklar om du vill skapa en virtuell dator från avbildningen. Annars använder du en generaliserad avbildnings version och anger administratörens användar namn, lösen ord eller SSH-nycklar.

**Meddelande**: den *obligatoriska parametern osProfile saknas (null).*  
**Orsak**: den virtuella datorn skapas från en generaliserad avbildning och saknar administratörs användar namn, lösen ord eller SSH-nycklar. Eftersom generaliserade avbildningar inte behåller administratörens användar namn, lösen ord eller SSH-nycklar, måste dessa fält anges när en virtuell dator eller skalnings uppsättning skapas.  
**Lösning**: Ange administratörens användar namn, lösen ord eller SSH-nycklar eller Använd en specialiserad avbildnings version.

**Meddelande**: *det går inte att skapa Galleri avbildnings Version från: <resourceID \> eftersom operativ systemets tillstånd i den överordnade Galleri avbildningen (specialiserad) inte är generaliserad.*  
**Orsak**: avbildnings versionen skapas från en generaliserad källa, men dess överordnade definition är specialiserad.  
**Lösning**: skapa avbildnings versionen med hjälp av en specialiserad källa eller Använd en överordnad definition som är generaliserad.

**Meddelande**: *det går inte att uppdatera den virtuella datorns skalnings uppsättning <vmssName \> eftersom det aktuella operativ systemets tillstånd för VM Scale set är generaliserat, vilket skiljer sig från det uppdaterade Galleri avbildnings operativ systemets tillstånd som är specialiserat.*  
**Orsak**: den aktuella käll avbildningen för skalnings uppsättningen är en generaliserad käll avbildning, men den uppdateras med en anpassad källbild. Den aktuella käll avbildningen och den nya käll avbildningen för en skalnings uppsättning måste vara i samma tillstånd.  
**Lösning**: om du vill uppdatera skalnings uppsättningen använder du en generaliserad avbildnings version.

**Meddelande**: *disk krypterings uppsättning <diskEncryptionSetId \> i det delade avbildnings galleriet <versionId \> tillhör prenumerations <subscriptionId1 \> och kan inte användas med resursen i prenumerationen <subscriptionId2 \>*  
**Orsak**: disk krypterings uppsättningen som används för att kryptera avbildnings versionen finns i en annan prenumeration än den prenumeration som är värd för avbildnings versionen.  
**Lösning**: Använd samma prenumeration för avbildnings versionen och disk krypterings uppsättningen.

**Meddelande**: *skapandet av den virtuella datorns eller virtuella datorns skalnings uppsättning tar lång tid.*  
**Lösning**: kontrol lera att **OSType** för den avbildnings version som du försöker skapa den virtuella datorn eller den virtuella datorns skalnings uppsättning från har samma **OSType** som den källa som du använde för att skapa avbildnings versionen. 

## <a name="creating-a-disk-from-an-image-version"></a>Skapa en disk från en avbildnings version ##

**Meddelande**: *värdet för parametern imageReference är ogiltigt.*  
**Orsak**: du försökte exportera från en sig-avbildnings version till en disk men använde en LUN-position som inte finns på avbildningen.    
**Lösning**: kontrol lera avbildnings versionen för att se vilka LUN-positioner som används.

## <a name="sharing-resources"></a>Dela resurser

Delning av avbildnings galleriet, bild definitionen och avbildnings versions resurserna i prenumerationer aktive ras med [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Hastighet för replikering

Använd flaggan **--Expand ReplicationStatus** för att kontrol lera om replikeringen till alla angivna mål regioner har avslut ATS. Om inte, vänta i upp till sex timmar innan jobbet har slutförts. Om det Miss lyckas utlöser du kommandot igen för att skapa och replikera avbildnings versionen. Om det finns många mål regioner som avbildnings versionen replikeras till bör du överväga att utföra replikeringen i faser.

## <a name="azure-limits-and-quotas"></a>Begränsningar och kvoter för Azure 

[Azure-gränser och kvoter](../azure-resource-manager/management/azure-subscription-service-limits.md) gäller för alla delade avbildnings gallerier, avbildnings definitioner och avbildnings versions resurser. Se till att du ligger inom gränserna för dina prenumerationer. 


## <a name="next-steps"></a>Nästa steg

Lär dig mer om [delade avbildnings gallerier](./shared-image-galleries.md).