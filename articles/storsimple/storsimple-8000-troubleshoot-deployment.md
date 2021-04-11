---
title: Felsök distributions problem i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du diagnostiserar och åtgärdar fel som uppstår när du först distribuerar StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566353"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Felsök problem med distribution av StorSimple-enheter
## <a name="overview"></a>Översikt
Den här artikeln innehåller användbara fel söknings anvisningar för din Microsoft Azure StorSimple-distribution. Det beskriver vanliga problem, möjliga orsaker och rekommenderade steg som hjälper dig att lösa problem som kan uppstå när du konfigurerar StorSimple. 

Den här informationen gäller både fysisk enhet i StorSimple 8000-serien och StorSimple Cloud Appliance.

> [!NOTE]
> Enhets konfiguration – problem som kan uppstå när du distribuerar enheten för första gången, eller som kan ske senare, när enheten fungerar. Den här artikeln fokuserar på fel sökning av distributions problem vid första tidpunkten. Om du vill felsöka en fungerande enhet går du till [Använd diagnostikverktyget för att felsöka en fungerande enhet](storsimple-8000-diagnostics.md).

Den här artikeln beskriver också verktyg för fel sökning av StorSimple-distributioner och innehåller ett exempel på steg-för-steg-fel sökning.

## <a name="first-time-deployment-issues"></a>Distributions problem vid första tidpunkten
Om du stöter på ett problem när du distribuerar enheten för första gången bör du tänka på följande:

* Om du felsöker en fysisk enhet måste du kontrol lera att maskin varan har installerats och kon figurer ATS enligt beskrivningen i [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
* Kontrol lera krav för distribution. Se till att du har all information som beskrivs i [Check lista för distributions konfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Läs igenom StorSimple-versionen för att se om problemet beskrivs. Viktig information innehåller lösningar för kända installations problem. 

Under enhets distributionen inträffar de vanligaste problemen som användarna möter när de kör installations guiden och när de registrerar enheten via Windows PowerShell för StorSimple. (Du använder Windows PowerShell för StorSimple för att registrera och konfigurera din StorSimple-enhet. Mer information om enhets registrering finns i [steg 3: Konfigurera och registrera din enhet via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

I följande avsnitt får du hjälp att lösa problem som du stöter på när du konfigurerar StorSimple-enheten för första gången.

## <a name="first-time-setup-wizard-process"></a>Installations guiden för första gången
I följande steg sammanfattas processen för installations guiden. Detaljerad installations information finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

1. Kör cmdleten [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) för att starta installations guiden som hjälper dig att utföra de återstående stegen. 
2. Konfigurera nätverket: med installations guiden kan du konfigurera nätverks inställningar för nätverks gränssnittet DATA 0 på din StorSimple-enhet. Följande inställningar ingår:
   * Virtuell IP-adress (VIP), nätmask och gateway – cmdleten [set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) körs i bakgrunden. Den konfigurerar IP-adressen, nät masken och gatewayen för nätverks gränssnittet DATA 0 på din StorSimple-enhet.
   * Primär DNS-Server – cmdleten [set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) körs i bakgrunden. Den konfigurerar DNS-inställningarna för din StorSimple-lösning.
   * NTP-server – cmdleten [set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) körs i bakgrunden. Den konfigurerar NTP-serverinställningar för din StorSimple-lösning.
   * Valfri webbproxy – cmdleten [set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) körs i bakgrunden. Den anger och aktiverar webbproxy-konfigurationen för din StorSimple-lösning.
3. Konfigurera lösen ordet: nästa steg är att konfigurera enhetens administratörs lösen ord.
   Enhetens administratörs lösen ord används för att logga in på enheten. Enheten standardlösenord är **Password1**.
        
     > [!IMPORTANT]
     > Lösen ord samlas in före registreringen, men tillämpas först när enheten har registrerats. Om det uppstår ett problem med att använda ett lösen ord uppmanas du att ange lösen ordet igen tills lösen orden som krävs (som uppfyller komplexitets kraven) samlas in.
     
4. Registrera enheten: det sista steget är att registrera enheten med StorSimple Enhetshanteraren-tjänsten som körs i Microsoft Azure. Registreringen kräver att du [hämtar tjänst registrerings nyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) från Azure Portal och anger den i installations guiden. **När enheten har registrerats får du en krypterings nyckel för tjänst data. Se till att behålla krypterings nyckeln på en säker plats eftersom det krävs för att registrera alla framtida enheter med tjänsten.**

## <a name="common-errors-during-device-deployment"></a>Vanliga fel vid enhets distribution
Följande tabell visar vanliga fel som du kan stöta på när du:

* Konfigurera de nätverks inställningar som krävs.
* Konfigurera de valfria webbproxy-inställningarna.
* Konfigurera enhetens administratörs lösen ord.
* Registrera enheten.

## <a name="errors-during-the-required-network-settings"></a>Fel under de nödvändiga nätverks inställningarna
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: det här kommandot kan endast köras på den aktiva styrenheten. |Konfigurationen utfördes på den passiva styrenheten. |Kör det här kommandot från den aktiva styrenheten. Mer information finns i [identifiera en aktiv kontrollant på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: enheten är inte klar. |Det finns problem med nätverks anslutningen på DATA 0. |Kontrol lera den fysiska nätverks anslutningen på DATA 0. |
| 3 |Invoke-HcsSetupWizard: det finns en IP-adresskonflikt med ett annat system i nätverket (undantag från HRESULT: 0x80070263). |Den angivna IP-adressen för DATA 0 används redan av ett annat system. |Ange en ny IP-adress som inte används. |
| 4 |Invoke-HcsSetupWizard: en kluster resurs misslyckades. (Undantag från HRESULT: 0x800713AE). |Dubblett av VIP. Den angivna IP-adressen används redan. |Ange en ny IP-adress som inte används. |
| 5 |Invoke-HcsSetupWizard: ogiltig IPv4-adress. |IP-adressen har ett felaktigt format. |Kontrol lera formatet och ange din IP-adress igen. Mer information finns i [IPv4-adressering][1]. |
| 6 |Invoke-HcsSetupWizard: ogiltig IPv6-adress. |IP-adressen har ett felaktigt format. |Kontrol lera formatet och ange din IP-adress igen. Mer information finns i [IPv6-adressering][2]. |
| 7 |Invoke-HcsSetupWizard: det finns inga fler slut punkter tillgängliga från slut punkts mappningen. (Undantag från HRESULT: 0x800706D9) |Kluster funktionen fungerar inte. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fel under de valfria webbproxy-inställningarna
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: ogiltig parameter (undantag från HRESULT: 0x80070057) |En av de parametrar som angavs för proxyinställningarna är inte giltig. |URI: n har inte angetts i rätt format. Använd följande format: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC-servern är inte tillgänglig (undantag från HRESULT: 0x800706ba) |Rotor saken är något av följande:<ol><li>Klustret är inte igång.</li><li>Den passiva styrenheten kan inte kommunicera med den aktiva styrenheten och kommandot körs från passiv styrenhet.</li></ol> |Beroende på rotor saken:<ol><li>[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att se till att klustret är igång.</li><li>Kör kommandot från den aktiva kontroll enheten. Om du vill köra kommandot från den passiva styrenheten måste du se till att den passiva styrenheten kan kommunicera med den aktiva styrenheten. Du måste [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om anslutningen är bruten.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC-anrop misslyckades (undantag från HRESULT: 0x800706be) |Klustret är nere. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att se till att klustret är igång. |
| 4 |Invoke-HcsSetupWizard: kluster resursen hittades inte (undantag från HRESULT: 0x8007138f) |Det gick inte att hitta kluster resursen. Detta kan inträffa när installationen inte var korrekt. |Du kan behöva återställa enheten till fabriks inställningarna. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att skapa en kluster resurs. |
| 5 |Invoke-HcsSetupWizard: kluster resursen är inte online (undantag från HRESULT: 0x8007138c) |Kluster resurserna är inte online. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-related-to-device-administrator-password"></a>Fel som rör enhetens administratörs lösen ord
Standard lösen ordet för enhets administratören är **Password1**. Lösen ordet upphör att gälla efter den första inloggningen. Därför måste du använda installations guiden för att ändra den. Du måste ange ett nytt enhets administratörs lösen ord när du registrerar enheten för första gången. 

Kontrol lera att lösen orden uppfyller följande krav:

* Enhetens administratörs lösen ord ska vara mellan 8 och 15 tecken långt.
* Lösen ord ska innehålla tre av följande tecken typer: gemener, versaler, siffror och Special. 
* Ditt lösen ord får inte vara detsamma som de senaste 24 lösen orden.

Tänk också på att lösen ord upphör att gälla varje år och bara kan ändras efter att enheten har registrerats. Om registreringen Miss lyckas av någon anledning ändras inte lösen orden.

Om du vill ha mer information om enhets administratörs lösen ord går du till [Använd tjänsten StorSimple Enhetshanteraren för att ändra StorSimple-lösenordet](storsimple-8000-change-passwords.md).

Ett eller flera av följande fel kan uppstå när du konfigurerar enhets administratören och StorSimple Snapshot Manager lösen ord.

| Nej. | Felmeddelande | Rekommenderad åtgärd |
| --- | --- | --- |
| 1 |Lösen ordet överskrider den maximala längden. |Enhetens administratörs lösen ord måste vara mellan 8 och 15 tecken långt. |
| 2 |Lösen ordet uppfyller inte den begärda längden. |Enhetens administratörs lösen ord måste vara mellan 8 och 15 tecken långt.|
| 3 |Lösen ordet måste innehålla gemena tecken. |Lösen ord måste innehålla 3 av följande 4 tecken typer: gemener, versaler, siffror och Special. Se till att ditt lösen ord uppfyller dessa krav. |
| 4 |Lösen ordet måste innehålla numeriska tecken. |Lösen ord måste innehålla 3 av följande 4 tecken typer: gemener, versaler, siffror och Special. Se till att ditt lösen ord uppfyller dessa krav. |
| 5 |Lösen ordet måste innehålla specialtecken. |Lösen ord måste innehålla 3 av följande 4 tecken typer: gemener, versaler, siffror och Special. Se till att ditt lösen ord uppfyller dessa krav. |
| 6 |Lösen ordet måste innehålla 3 av följande 4 tecken typer: versaler, gemener, siffror och Special. |Lösen ordet innehåller inte de tecken typer som krävs. Se till att ditt lösen ord uppfyller dessa krav. |
| 7 |Parametern matchar inte bekräftelsen. |Kontrol lera att ditt lösen ord uppfyller alla krav och att du har angett rätt. |
| 8 |Lösen ordet kan inte matcha standardvärdet. |Standard lösen ordet är *Password1*. Du måste ändra det här lösen ordet när du har loggat in för första gången. |
| 9 |Det angivna lösen ordet matchar inte enhetens lösen ord. Skriv in lösen ordet igen. |Kontrol lera lösen ordet och ange det igen. |

Lösen ord samlas in innan enheten registreras, men används endast efter lyckad registrering. Arbets flödet för lösen ords återställning kräver att enheten registreras.

> [!IMPORTANT]
> I allmänhet försöker programmet upprepade gånger att samla in lösen ordet tills det lyckas om ett försök att använda ett lösen ord Miss lyckas. I sällsynta fall kan inte lösen ordet tillämpas. I så fall kan du registrera enheten och fortsätta, men lösen orden kommer inte att ändras. Du kan ändra enhetens administratörs lösen ord efter registreringen från Azure Portal.


Du kan återställa lösen ordet i Azure Portal via tjänsten StorSimple Enhetshanteraren. Mer information finns i [Ändra enhetens administratörs lösen ord](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Fel under enhetsregistreringen
Du kan använda tjänsten StorSimple Enhetshanteraren som körs i Microsoft Azure för att registrera enheten. Ett eller flera av följande problem kan uppstå under enhets registreringen.

| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Fel 350027: det gick inte att registrera enheten med StorSimple-Enhetshanteraren. | |Vänta några minuter och försök sedan igen. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om problemet kvarstår. |
| 2 |Fel 350013: ett fel uppstod när enheten registrerades. Detta kan bero på en felaktig tjänst registrerings nyckel. | |Registrera enheten igen med rätt tjänst registrerings nyckel. Mer information finns i [Hämta tjänst registrerings nyckeln.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Fel 350063: autentiseringen till StorSimple Enhetshanteraren tjänsten skickades men registreringen misslyckades. Försök igen om en stund. |Det här felet anger att autentiseringen med ACS har slutförts men att registrerings anropet till tjänsten misslyckades. Detta kan vara ett resultat av ett sporadiskt nätverks fel. |Om problemet kvarstår [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fel 350049: det gick inte att nå tjänsten under registreringen. |När anropet görs till tjänsten tas ett webb undantag emot. I vissa fall kan detta åtgärdas genom att försöka igen senare. |Kontrol lera IP-adressen och DNS-namnet och försök sedan igen. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om problemet kvarstår. |
| 5 |Fel 350031: enheten har redan registrerats. | |Ingen åtgärd krävs. |
| 6 |Fel 350016: enhets registrering misslyckades. | |Kontrol lera att registrerings nyckeln är korrekt. |
| 7 |Invoke-HcsSetupWizard: ett fel uppstod när enheten registrerades. Detta kan bero på felaktig IP-adress eller DNS-namn. Kontrol lera dina nätverks inställningar och försök igen. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om problemet kvarstår. (Fel 350050) |Kontrol lera att enheten kan pinga utanför nätverket. Om du inte har någon anslutning till nätverket utanför nätverket kan registreringen Miss lyckas med det här felet. Det här felet kan vara en kombination av ett eller flera av följande:<ul><li>Felaktig IP</li><li>Felaktigt undernät</li><li>Felaktig gateway</li><li>Felaktiga DNS-inställningar</li></ul> |Se stegen i [fel söknings exempel för steg-för-steg](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: den aktuella åtgärden misslyckades på grund av ett internt tjänst fel [0x1FBE2]. Försök igen om en stund. Om problemet kvarstår kontaktar du Microsoft Support. |Detta är ett allmänt fel som har utlösts för alla användares osynliga fel från tjänst eller agent. Den vanligaste orsaken kan vara att ACS-autentiseringen har misslyckats. En möjlig orsak till felet är att det finns problem med att NTP-serverkonfigurationen och tiden på enheten inte är korrekt inställda. |Korrigera tiden (om det finns problem) och försök sedan att registrera igen. Om du använder kommandot Set-HcsSystem-timezone för att justera tids zonen måste du skriva varje ord i tids zonen till versaler (t. ex. "Pacific Standard Time").  Om problemet kvarstår [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |
| 9 |Varning: det gick inte att aktivera enheten. Enhets administratören och StorSimple Snapshot Manager lösen ord har inte ändrats. |Om registreringen Miss lyckas ändras inte enhets administratörs-och StorSimple Snapshot Manager lösen ord. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Verktyg för felsökning av StorSimple-distributioner
StorSimple innehåller flera verktyg som du kan använda för att felsöka din StorSimple-lösning. Dessa verktyg innefattar:

* Support paket och enhets loggar.
* -Cmdletar utformade för fel sökning.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Support paket och enhets loggar som är tillgängliga för fel sökning
Ett support paket innehåller alla relevanta loggar som kan hjälpa Microsoft Supports teamet med fel sökning av enhets problem. Du kan använda Windows PowerShell för StorSimple för att generera ett krypterat support paket som du sedan kan dela med support personal.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Visa loggarna eller innehållet i support paketet
1. Använd Windows PowerShell för StorSimple för att generera ett support paket enligt beskrivningen i [skapa och hantera ett support paket](storsimple-8000-create-manage-support-package.md).
2. Ladda ned [krypterings skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klient datorn.
3. Använd den här [steg-för-steg-proceduren](storsimple-8000-create-manage-support-package.md#edit-a-support-package) för att öppna och dekryptera support paketet.
4. De dekrypterade support paket loggarna är i ETW/etvx-format. Du kan utföra följande steg för att visa de här filerna i Windows Loggboken:
   
   1. Kör kommandot **eventvwr** på Windows-klienten för att starta Loggboken.
   2. I rutan **åtgärder** klickar du på **Öppna Sparad logg** och pekar på loggfilerna i etvx/ETW-format (support paketet). Nu kan du Visa filen. När du har öppnat filen kan du högerklicka på den och spara filen som text.
      
      > [!IMPORTANT]
      > Du kan också använda cmdleten **Get-WinEvent** för att öppna dessa filer i Windows PowerShell. Mer information finns i [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) i referens dokumentationen för Windows PowerShell-cmdleten.
     
5. När loggarna öppnas i Loggboken letar du efter följande loggar som innehåller problem som rör enhets konfigurationen:
   
   * hcs_pfconfig/Operational-logg
   * hcs_pfconfig/config
6. I loggfilerna söker du efter strängar som är relaterade till de cmdletar som anropas av installations guiden. Se [installations guiden för första gången](#first-time-setup-wizard-process) för att få en lista över dessa cmdlets.
7. Om du inte kan räkna ut orsaken till problemet kan du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Följ stegen i [skapa en support förfrågan](storsimple-8000-contact-microsoft-support.md#create-a-support-request) när du kontaktar Microsoft Support för att få hjälp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets som är tillgängliga för fel sökning
Använd följande Windows PowerShell-cmdlets för att identifiera anslutnings fel.

* `Get-NetAdapter`: Använd denna cmdlet för att identifiera hälso tillståndet för nätverks gränssnitt.
* `Test-Connection`: Använd denna cmdlet för att kontrol lera nätverks anslutningen i och utanför nätverket.
* `Test-HcsmConnection`: Använd denna cmdlet för att kontrol lera anslutningen till en korrekt registrerad enhet.
* `Sync-HcsTime`: Använd den här cmdleten för att Visa enhets tiden och framtvinga en tidssynkronisering med NTP-servern.
* `Enable-HcsPing` och `Disable-HcsPing` : Använd dessa cmdlets för att tillåta att värdarna pingar nätverks gränssnitten på din StorSimple-enhet. Som standard svarar inte StorSimple-nätverks gränssnitten på ping-begäranden.
* `Trace-HcsRoute`: Använd denna cmdlet som väg spårnings verktyg. Den skickar paket till varje router på vägen till ett slutgiltigt mål under en viss tids period och beräknar sedan resultatet baserat på de paket som returneras från varje hopp. Eftersom `Trace-HcsRoute` visar graden av paket förlust vid en viss router eller länk, kan du hitta vilka routrar eller länkar som kan orsaka nätverks problem.
* `Get-HcsRoutingTable`: Använd den här cmdleten för att visa den lokala IP-routningstabellen.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Felsöka med Get-NetAdapter cmdlet
När du konfigurerar nätverks gränssnitt för en första enhets distribution är maskin varu statusen inte tillgänglig i StorSimple-Enhetshanteraren tjänstens användar gränssnitt eftersom enheten ännu inte har registrerats med tjänsten. **Maskin varu hälso** bladet kanske inte alltid alltid motsvarar enhetens status, särskilt om det finns problem som påverkar synkroniseringen av tjänsten. I dessa fall kan du använda `Get-NetAdapter` cmdleten för att fastställa hälso tillstånd och status för nätverks gränssnitten.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Visa en lista över alla nätverkskort på enheten
1. Starta Windows PowerShell för StorSimple och skriv sedan `Get-NetAdapter` . 
2. Använd utdata från `Get-NetAdapter` cmdleten och följande rikt linjer för att förstå nätverks gränssnittets status.
   
   * Om gränssnittet är felfritt och aktiverat visas **status för** **ifIndex** .
   * Om gränssnittet är felfritt men inte är fysiskt anslutet (av en nätverks kabel), visas **ifIndex** som **inaktive rad**.
   * Om gränssnittet är felfritt men inte aktiverat visas **ifIndex** status som **NotPresent**.
   * Om gränssnittet inte finns visas det inte i listan. Användar gränssnittet för StorSimple Enhetshanteraren-tjänsten kommer fortfarande att visa det här gränssnittet i ett felaktigt tillstånd.

Mer information om hur du använder den här cmdleten finns i [Get-netadapter](/powershell/module/netadapter/get-netadapter) i Windows PowerShell-cmdlet-referensen.

I följande avsnitt visas exempel på utdata från `Get-NetAdapter` cmdleten.

 I de här exemplen var Controller 0 den passiva styrenheten och konfigurerades på följande sätt:

* DATA 0, DATA 1, DATA 2 och DATA 3 nätverks gränssnitt fanns på enheten.
* Det finns inga nätverks gränssnitts kort för DATA 4 och DATA 5. de visas därför inte i utdata.
* DATA 0 har Aktiver ATS.

Kontroll enhet 1 var den aktiva kontrollanten och konfigurerades enligt följande:

* DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 och DATA 5 nätverks gränssnitt fanns på enheten.
* DATA 0 har Aktiver ATS.

**Exempel utdata – kontrollant 0**

Följande exempel data är utdata från Controller 0 (den passiva styrenheten). DATA 1, DATA 2 och DATA 3 är inte anslutna. DATA 4 och DATA 5 visas inte eftersom de inte finns på enheten.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Exempel utdata – kontrollant 1**

Följande exempel data är utdata från styrenhet 1 (den aktiva styrenheten). Endast nätverks gränssnittet för DATA 0 på enheten är konfigurerat och fungerar.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Felsöka med Test-Connection cmdlet
Du kan använda `Test-Connection` cmdleten för att avgöra om din StorSimple-enhet kan ansluta till nätverket utanför nätverket. Om alla nätverks parametrar, inklusive DNS, är korrekt konfigurerade i installations guiden, kan du använda `Test-Connection` cmdleten för att pinga en känd adress utanför nätverket, till exempel Outlook.com.

Om ping-cmdleten är inaktive rad bör du aktivera ping för användning vid fel sökning av anslutnings problem.

Se följande exempel på utdata från `Test-Connection` cmdleten.

> [!NOTE]
> I det första exemplet är enheten konfigurerad med en felaktig DNS. I det andra exemplet är DNS rätt.

**Exempel på utdata – felaktig DNS**

Följande exempel innehåller inte några utdata för IPV4-och IPV6-adresser, vilket indikerar att DNS inte är löst. Det finns ingen anslutning till det externa nätverket och en korrekt DNS måste anges.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Exempel på utdata – rätt DNS**

I följande exempel returnerar DNS IPV4-adressen som anger att DNS är korrekt konfigurerat. Utdata bekräftar att det finns en anslutning till nätverket utanför nätverket.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Felsöka med Test-HcsmConnection cmdlet
Använd `Test-HcsmConnection` cmdleten för en enhet som redan är ansluten till och registrerad med din StorSimple Enhetshanteraren-tjänst. Med den här cmdleten kan du kontrol lera anslutningen mellan en registrerad enhet och motsvarande StorSimple Enhetshanteraren-tjänst. Du kan köra det här kommandot på Windows PowerShell för StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Köra cmdleten Test-HcsmConnection
1. Kontrol lera att enheten är registrerad.
2. Kontrol lera enhetens status. Om enheten är inaktive rad, i underhålls läge eller offline kan du se något av följande fel:
   
   * Felkod. CiSDeviceDecommissioned: visar att enheten är inaktive rad.
   * Felkod. DeviceNotReady: indikerar att enheten är i underhålls läge.
   * Felkod. DeviceNotReady: indikerar att enheten inte är online.
3. Verifiera att tjänsten StorSimple Enhetshanteraren körs (Använd cmdleten [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) ). Om tjänsten inte körs kan du se följande fel:
   
   * ErrorCode. CiSApplianceAgentNotOnline
   * ErrorCode. CisPowershellScriptHcsError: indikerar att ett undantag inträffade när du körde Get-ClusterResource.
4. Kontrol lera Access Control Service (ACS)-token. Om det ger upphov till ett webb undantag kan det bero på ett Gateway-problem, att en proxy-autentisering saknas, att en felaktig DNS eller ett autentiseringsfel uppstår. Följande fel kan visas:
   
   * ErrorCode. CiSApplianceGateway: anger ett HttpStatusCode. BadGateway-undantag: tjänsten för namn matchning kunde inte matcha värd namnet.
   * ErrorCode. CiSApplianceProxy: anger ett HttpStatusCode. ProxyAuthenticationRequired-undantag (HTTP-status kod 407): klienten kunde inte autentiseras med proxyservern.
   * ErrorCode. CiSApplianceDNSError: anger ett WebExceptionStatus. NameResolutionFailure-undantag: tjänsten för namn matchning kunde inte matcha värd namnet.
   * Felkod. CiSApplianceACSError: indikerar att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om den inte genererar ett webb undantag söker du efter ErrorCode. CiSApplianceFailure, vilket tyder på att installationen misslyckades.
5. Kontrol lera anslutningen till moln tjänsten. Om tjänsten genererar ett webb undantag kan följande fel meddelande visas:
   
   * ErrorCode. CiSApplianceGateway: anger ett HttpStatusCode. BadGateway-undantag: en mellanliggande proxyserver tog emot en felaktig begäran från en annan proxy eller från den ursprungliga servern.
   * ErrorCode. CiSApplianceProxy: anger ett HttpStatusCode. ProxyAuthenticationRequired-undantag (HTTP-status kod 407): klienten kunde inte autentiseras med proxyservern.
   * ErrorCode. CiSApplianceDNSError: anger ett WebExceptionStatus. NameResolutionFailure-undantag: tjänsten för namn matchning kunde inte matcha värd namnet.
   * Felkod. CiSApplianceACSError: indikerar att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om den inte genererar ett webb undantag söker du efter ErrorCode. CiSApplianceSaasServiceError, vilket tyder på ett problem med tjänsten StorSimple Enhetshanteraren.
6. Kontrol lera Azure Service Bus anslutningen. ErrorCode. CiSApplianceServiceBusError anger att enheten inte kan ansluta till Service Bus.

Loggfilerna CiSCommandletLog0Curr. errlog och CiSAgentsvc0Curr. errlog innehåller mer information, till exempel information om undantag.

Mer information om hur du använder cmdleten finns i [test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) i referens dokumentationen för Windows PowerShell.

> [!IMPORTANT]
> Du kan köra denna cmdlet för både den aktiva och den passiva styrenheten.

Se följande exempel på utdata från `Test-HcsmConnection` cmdleten.

**Exempel på utdata – enheten har registrerats med StorSimple Update 3**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Exempel på utdata – offline-enhet** 

Det här exemplet är från en enhet som har statusen **offline** i Azure Portal.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Enheten kunde inte ansluta med den aktuella webbproxy-konfigurationen. Det kan finnas ett problem med webbproxy-konfigurationen eller ett problem med nätverks anslutningen. I det här fallet bör du kontrol lera att webbproxyinställningarna är korrekta och att webbproxyservrarna är online och går att komma åt.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Felsöka med Sync-HcsTime cmdlet
Använd den här cmdleten för att Visa enhets tiden. Om enhetens tid har en förskjutning med NTP-servern kan du använda denna cmdlet för att tvinga fram en synkronisering av tiden med NTP-servern.
- Om förskjutningen mellan enheten och NTP-servern är större än 5 minuter visas en varning. 
- Om förskjutningen överskrider 15 minuter kopplas enheten från. Du kan fortfarande använda denna cmdlet för att tvinga fram en tidssynkronisering. 
- Men om förskjutningen överskrider 15 timmar kommer du inte att kunna Framtvinga synkroniseringen av tiden och ett fel meddelande visas.

**Exempel utdata – Tvingad tidssynkronisering med Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Felsöka med Enable-HcsPing-och Disable-HcsPing-cmdletar
Använd dessa cmdlets för att säkerställa att nätverks gränssnitten på enheten svarar på ICMP-ping-begäranden. Som standard svarar inte StorSimple-nätverks gränssnitten på ping-begäranden. Att använda denna cmdlet är det enklaste sättet att veta om din enhet är online och kan kontaktas.

**Exempel på utdata – Enable-HcsPing och Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Felsöka med Trace-HcsRoute cmdlet
Använd denna cmdlet som väg spårnings verktyg. Den skickar paket till varje router på vägen till ett slutgiltigt mål under en viss tids period och beräknar sedan resultatet baserat på de paket som returneras från varje hopp. Eftersom cmdleten visar graden av paket förlust vid en viss router eller länk, kan du hitta vilka routrar eller länkar som kan orsaka nätverks problem.

**Exempel på utdata som visar hur du spårar vägen för ett paket med trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Felsöka med Get-HcsRoutingTable cmdlet
Använd den här cmdleten för att Visa routningstabellen för din StorSimple-enhet. En routningstabell är en uppsättning regler som kan hjälpa dig att avgöra var data paket som reser över ett Internet Protocol (IP) nätverk kommer att dirigeras om.

Routningstabellen visar gränssnitten och gatewayen som dirigerar data till de angivna nätverken. Det ger även vägens mått, som är besluts fattaren för den sökväg som används för att uppnå ett visst mål. Ju lägre vägens mått, desto högre prioritet.

Anta till exempel att du har två nätverks gränssnitt, DATA 2 och DATA 3, anslutna till Internet. Om routing-måtten för DATA 2 och DATA 3 är 15 respektive 261 är DATA 2, med det lägre måttet routning, det önskade gränssnittet som används för att komma åt Internet.

Om du kör uppdatering 1 på din StorSimple-enhet har nätverks gränssnittet för DATA 0 högsta prioritet för moln trafiken. Med den här konfigurationen, även om det finns andra molnbaserade gränssnitt, dirigeras merparten av moln trafiken genom DATA 0.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

Om du kör `Get-HcsRoutingTable` cmdleten utan att ange några parametrar (som följande exempel visar), kommer cmdleten att mata in både IPv4-och IPv6-vägvals tabeller. Du kan också ange `Get-HcsRoutingTable -IPv4` eller `Get-HcsRoutingTable -IPv6`  för att hämta en relevant routningstabell.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Steg-för-steg-StorSimple fel söknings exempel
I följande exempel visas steg-för-steg-felsökning av en StorSimple-distribution. I exempel scenariot Miss lyckas enhets registreringen med ett fel meddelande som anger att nätverks inställningarna eller DNS-namnet är felaktigt.

Det fel meddelande som returnerades är:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

Felet kan bero på något av följande problem:

* Felaktig maskin varu installation
* Fel nätverks gränssnitt (n)
* Felaktig IP-adress, nätmask, Gateway, primär DNS-server eller webbproxy
* Felaktig registrerings nyckel
* Felaktiga brand Väggs inställningar

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Hitta och åtgärda problem med enhets registrering
1. Kontrol lera enhets konfigurationen: på den aktiva styrenheten kör du `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > Installations guiden måste köras på den aktiva kontroll enheten. Kontrol lera att du är ansluten till den aktiva kontrollanten genom att titta på banderollen som visas i serie konsolen. Banderollen visar om du är ansluten till kontroll enhet 0 eller styrenhet 1 och om styrenheten är aktiv eller passiv. Mer information finns i [identifiera en aktiv kontrollant på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Kontrol lera att enheten är ordentligt kabelansluten: kontrol lera nätverks kablarna på enhetens bak plan. Kablaget är speciellt för enhets modellen. Mer information finns i [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Om du använder 10 GbE-nätverksanslutningar måste du använda de tillhandahållna QSFP-SFP-korten och SFP-kablar. Mer information finns i [listan över kablar, växlar och Sänd tagare som rekommenderas för 10 GbE-portar](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verifiera nätverks gränssnittets hälso tillstånd:
   
   * Använd Get-NetAdapter-cmdlet för att identifiera hälso tillståndet för nätverks gränssnitten för DATA 0. 
   * Om länken inte fungerar `ifindex` indikerar statusen att gränssnittet är nere. Du måste sedan kontrol lera nätverks anslutningen till porten och till-växeln. Du kommer också att behöva utesluta Felaktiga kablar. 
   * Om du misstänker att DATA 0-porten på den aktiva styrenheten har misslyckats, kan du bekräfta att genom att ansluta till DATA 0-porten på styrenhet 1. Koppla bort nätverks kabeln från enhetens bak enhet från styrenhet 0, Anslut kabeln till styrenhet 1 och kör sedan Get-NetAdapter cmdlet igen.
   
     [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg om data 0-porten på en styrenhet Miss lyckas. Du kan behöva ersätta kontroll enheten i systemet.
4. Kontrol lera anslutningen till växeln:
   
   * Kontrol lera att DATA 0 nätverks gränssnitt på styrenhet 0 och styrenhet 1 i den primära inne slutningen finns i samma undernät. 
   * Kontrol lera hubben eller routern. Normalt bör du ansluta båda styrenheterna till samma hubb eller router. 
   * Kontrol lera att de växlar som du använder för anslutningen har DATA 0 för båda styrenheterna i samma vLAN.
5. Eliminera eventuella användar fel:
   
   * Kör installations guiden igen (kör **Invoke-HcsSetupWizard**) och ange värdena igen för att se till att det inte finns några fel. 
   * Verifiera den registrerings nyckel som används. Samma registrerings nyckel kan användas för att ansluta flera enheter till en StorSimple Enhetshanteraren-tjänst. Använd proceduren i [Hämta tjänst registrerings nyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) för att kontrol lera att du använder rätt registrerings nyckel.
     
     > [!IMPORTANT]
     > Om du har flera tjänster som kör måste du kontrol lera att registrerings nyckeln för lämplig tjänst används för att registrera enheten. Om du har registrerat en enhet med fel StorSimple Enhetshanteraren tjänst måste du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få nästa steg. Du kanske måste utföra en fabriks återställning av enheten (vilket kan leda till data förlust) för att sedan ansluta den till den avsedda tjänsten.
     > 
     > 
6. Använd Test-Connection-cmdlet för att kontrol lera att du har anslutning till nätverket utanför nätverket. Mer information finns i [Felsöka med cmdleten Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Kontrol lera brand Väggs störningar. Om du har verifierat att den virtuella IP-adressen (VIP), undernät, gateway och DNS-inställningar är korrekta och du fortfarande ser anslutnings problem, är det möjligt att brand väggen blockerar kommunikationen mellan enheten och det externa nätverket. Se till att portarna 80 och 443 är tillgängliga på din StorSimple-enhet för utgående kommunikation. Mer information finns i [nätverks krav för din StorSimple-enhet](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Titta på loggarna. Gå till [support paket och enhets loggar som är tillgängliga för fel sökning](#support-packages-and-device-logs-available-for-troubleshooting).
9. Om föregående steg inte löser problemet kan du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder Diagnostic-verktyget för att felsöka en StorSimple-enhet](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)