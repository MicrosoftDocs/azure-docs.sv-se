---
title: Använd Azure Portal för att felsöka Azure Stack Edge Pro med GPU | Microsoft Docs
description: Beskriver hur du felsöker GPU-problem med Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 87e75d771c2cc269eaae81c2433f445eb65a17a9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314158"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Felsök problem med din Azure Stack Edge Pro GPU-enhet 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du felsöker problem med GPU-enheten för Azure Stack Edge Pro. 


## <a name="run-diagnostics"></a>Kör diagnostik

Du kan köra de diagnostiska testerna för att diagnostisera och felsöka eventuella enhetsfel. Utför följande steg i det lokala webbgränssnittet på din enhet för att köra diagnostiska tester.

1. I det lokala webbgränssnittet går du till **Felsökning > Diagnostiska tester**. Välj det test du vill köra och välj **Kör test**. Testet diagnostiserar eventuella eventuella problem med nätverks-, enhets-, webbproxy-, tid-eller moln inställningar. Du får ett meddelande om att enheten kör testerna.

    ![Välj test ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. När testerna har slutförts visas resultatet. 

    ![Visa test resultat](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Om testet inte kan genomföras visas en URL för rekommenderad åtgärd. Välj URL: en för att visa den rekommenderade åtgärden.
 
    ![Granska varningar för misslyckade tester](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Hämta supportpaket

Ett loggpaket består av alla relevanta loggar som kan hjälpa Microsoft Support att felsöka eventuella enhetsproblem. Du kan generera ett loggpaket via det lokala webbgränssnittet.

Utför följande steg för att hämta ett supportpaket. 

1. I det lokala webbgränssnittet går du till **Felsökning > Support**. Välj **skapa support paket**. Systemet börjar samla in supportpaket. Paketinsamlingen kan ta flera minuter.

    ![Välj Lägg till användare](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. När du har skapat support paketet väljer du **Hämta support paket**. Ett komprimerat paket har laddats ned till den sökväg som du har valt. Du kan extrahera paketet och visa systemloggfilerna.

    ![Välj Lägg till användare 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Samla in avancerade säkerhets loggar

De avancerade säkerhets loggarna kan vara program varu-eller maskin varu intrångs loggar för din Azure Stack Edge Pro-enhet.

### <a name="software-intrusion-logs"></a>Program varu intrångs loggar

Program varu intrång eller standard brand Väggs loggar samlas in för inkommande och utgående trafik. 

- När enheten är avbildad på fabriken är standard brand Väggs loggning aktive rad. Dessa loggar ingår som standard i support paketet när du skapar ett support paket via det lokala användar gränssnittet eller via Windows PowerShell-gränssnittet på enheten.

- Om det bara behövs brand Väggs loggar i support paketet för att granska eventuella program (NW)-intrång i enheten använder `-Include FirewallLog` du alternativet när du skapar support paketet. 

- Om inget särskilt alternativ för inkludering anges ingår brand Väggs loggen som standard i support paketet.

- I support paketet är brand Väggs loggen `pfirewall.log` och placerad i rotmappen. Här är ett exempel på program varu intrångs loggen för Azure Stack Edge Pro-enheten. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Maskin varu intrångs loggar

För att identifiera maskin varu intrång i enheten loggas alla chassi händelser som att öppna eller stänga chassit. 

- System händelse loggen från enheten läses med hjälp av `racadm` cmdleten. Dessa händelser filtreras sedan efter chassi-relaterade händelser i en `HWIntrusion.txt` fil.

- Om du bara vill hämta maskin varu intrångs loggen i support paketet använder `-Include HWSelLog` du alternativet när du skapar support paketet. 

- Om det inte finns något särskilt alternativ för att inkludera, ingår maskin varu intrångs loggen som standard i support paketet.

- I support paketet är maskin varu intrångs loggen `HWIntrusion.txt` och placerad i rotmappen. Här är ett exempel på maskin varu intrångs logg för Azure Stack Edge Pro-enheten. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Använd loggar för att felsöka

Eventuella fel som uppstod under uppladdnings- och uppdateringsprocesserna ingår i respektive felfiler.

1. Om du vill visa felfilerna går du till resursen och väljer resursen för att visa innehållet. 


2. Välj _Microsoft data Box Edge-mappen_. Den här mappen innehåller två undermappar:

    - Ladda upp mappen som innehåller loggfiler för uppladdningsfelen.
    - Uppdatera mappen för fel som uppstått under uppdateringen.

    Här är en exempelloggfil för uppdatering.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. När du ser ett fel i den här filen (markerat i exemplet), noterar du felkoden, vilken i det här fallet är 16001. Leta upp beskrivningen av den här felkoden mot följande felkodsreferens.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Felsöka med hjälp av fel listor

Fel listorna kompileras från identifierade scenarier och kan användas för självdiagnostik och fel sökning. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Här är de fel som kan visas under konfigurationen av Azure Resource Manager för åtkomst till enheten. 

| **Problem/fel** |  **Lösning** | 
|------------|-----------------|
|Allmänna frågor|<li>[Kontrol lera att gräns enheten är korrekt konfigurerad](#verify-the-device-is-configured-properly).<li> [Kontrol lera att klienten är korrekt konfigurerad](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: ett fel uppstod när begäran skickades.<br>På rad: 1 tecken: 1<br>+ Add-AzureRmEnvironment-Name Az3-ARMEndpoint " https://management.dbe ...|Det här felet innebär att din Azure Stack Edge Pro-enhet inte kan kontaktas eller har kon figurer ATS korrekt. Kontrol lera att gräns enheten och klienten är korrekt konfigurerade. Vägledning finns i raden **allmänna problem** i den här tabellen.|
|Tjänsten returnerade ett fel. Check InnerException för mer information: den underliggande anslutningen stängdes: det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen. |   Det här felet beror troligen på en eller flera åtgärder för att utföra egna certifikat på ett felaktigt sätt. [Här](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)kan du hitta vägledning. |
|Åtgärden returnerade en ogiltig status kod ' ServiceUnavailable ' <br> Svars status koden indikerar inte lyckad: 503 (tjänsten är inte tillgänglig). | Det här felet kan vara resultatet av något av dessa villkor.<li>ArmStsPool är i stoppat läge.</li><li>Någon av webbplatserna för Azure Resource Manager/säkerhetstoken-tjänster är nere.</li><li>Kluster resursen för Azure Resource Manager är nere.</li><br><strong>Obs:</strong> Det kan lösa problemet genom att starta om enheten, men du bör samla in support paketet så att du kan felsöka det ytterligare.|
|AADSTS50126: ogiltigt användar namn eller lösen ord.<br>Spårnings-ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Korrelations-ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Tidsstämpel: 2019-11-15 09:21:57Z: fjärrservern returnerade ett fel: (400) felaktig begäran.<br>På rad: 1 tecken: 1 |Det här felet kan vara resultatet av något av dessa villkor.<li>För ett ogiltigt användar namn och lösen ord kontrollerar du att kunden har ändrat lösen ordet från Azure Portal genom att följa stegen [här](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password) och sedan använda rätt lösen ord.<li>För ett ogiltigt klient-ID är klient-ID: t ett fast GUID och ska anges till `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: resursen är inaktive rad eller finns inte. Kontrol lera appens kod för att se till att du har angett den exakta resurs-URL: en för resursen som du försöker få åtkomst till.<br>Spårnings-ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Korrelations-ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 tidsstämpel: 2019-11-18 07:00:51Z: fjärrservern returnerade ett fel: (400) dåligt |Resurs slut punkterna som används i `Add-AzureRmEnvironment` kommandot är felaktiga.|
|Det gick inte att hämta slut punkter från molnet.<br>Kontrol lera att du har en nätverks anslutning. Fel information: HTTPSConnectionPool (värd = ' Management. dbg-of4k6suvm.microsoftdatabox.com ', Port = 30005): högsta antal återförsök har överskridits med URL:/metadata/endpoints? API-version = 2015-01-01 (orsakas av SSLError (SSLError ("felaktig hand skakning: fel ([(" SSL-rutiner "," tls_process_server_certificate "," certifikats verifiering misslyckades ")],),)) |Det här felet förekommer oftast i en Mac/Linux-miljö och beror på följande:<li>Ett PEM-format certifikat har inte lagts till i python-certifikatarkivet.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Kontrol lera att enheten är korrekt konfigurerad

1. I det lokala användar gränssnittet kontrollerar du att enhets nätverket är korrekt konfigurerat.

2. Kontrol lera att certifikaten har uppdaterats för alla slut punkter som anges [här](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Hämta slut punkten för Azure Resource Manager hantering och inloggning från **enhets** sidan i lokalt användar gränssnitt.

4. Kontrol lera att enheten är aktive rad och registrerad i Azure.


### <a name="verify-the-client-is-configured-properly"></a>Kontrol lera att klienten är korrekt konfigurerad

1. Kontrol lera att rätt PowerShell-version är installerad som anges [här](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. Kontrol lera att rätt PowerShell-moduler är installerade som anges [här](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Verifiera att Azure Resource Manager-och inloggnings slut punkter kan uppnås. Du kan prova att pinga slut punkterna. Exempel:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Om de inte går att komma åt lägger du till DNS/värd fil poster som anges [här](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Kontrol lera att klient certifikaten installeras som anges [här](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Om kunden använder PowerShell bör du aktivera fel söknings inställningen för att se detaljerade meddelanden genom att köra det här PowerShell-kommandot. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage på enhet 

Här är felen som rör Blob Storage på Azure Stack Edge Pro/Data Box Gateway Device.

| **Problem/fel** |  **Lösning** | 
|--------------------|-----------------|
|Det gick inte att hämta underordnade resurser. Värdet för ett av HTTP-huvudena har fel format.| I **Redigera** -menyn väljer du **mål Azure Stack API: er**. Starta sedan om Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på den här sökvägen: `C:\Windows\System32\drivers\etc\hosts` på Windows eller `/etc/hosts` i Linux.|
|Det gick inte att hämta underordnade resurser.<br> Information: självsignerat certifikat |Importera SSL-certifikatet för enheten till Azure Storage Explorer: <ol><li>Hämta certifikatet från Azure Portal. Mer information finns i [Hämta certifikatet](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Välj SSL-certifikat på **Redigera** -menyn och välj sedan **Importera certifikat**.</li></ol>|
|AzCopy-kommandot slutar svara i minuter innan det här felet visas:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på: `C:\Windows\System32\drivers\etc\hosts` .|
|AzCopy-kommandot slutar svara i minuter innan det här felet visas:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importera SSL-certifikatet för enheten till certifikat arkivet i systemet. Mer information finns i [Hämta certifikatet](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|AzCopy-kommandot verkar sluta svara i 20 minuter innan det här felet visas:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på: `/etc/hosts` .|
|AzCopy-kommandot verkar sluta svara i 20 minuter innan det här felet visas:<br>`Error parsing source location… The SSL connection could not be established`. |Importera SSL-certifikatet för enheten till certifikat arkivet i systemet. Mer information finns i [Hämta certifikatet](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|AzCopy-kommandot verkar sluta svara i 20 minuter innan det här felet visas:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på: `/etc/hosts` .|
|AzCopy-kommandot verkar sluta svara i 20 minuter innan det här felet visas: `Error parsing source location… The SSL connection could not be established` .|Importera SSL-certifikatet för enheten till certifikat arkivet i systemet. Mer information finns i [Hämta certifikatet](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Värdet för ett av HTTP-huvudena har fel format.|Den installerade versionen av Microsoft Azure Storages biblioteket för python stöds inte av Data Box-enhet. Se Azure Data Box Blob Storage-krav för versioner som stöds.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Innan du kör python anger du REQUESTS_CA_BUNDLE miljövariabeln till sökvägen till den base64-kodade SSL-certifikatfil (se hur du [hämtar certifikatet](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Exempel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternativt kan du lägga till certifikatet i systemets certifikat Arkiv och sedan ange miljövariabeln till sökvägen för arkivet. Till exempel på Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Anslutnings tiden är slut.|Logga in på Azure Stack Edge Pro och kontrol lera att den är olåst. När enheten startas om förblir den låst tills någon loggar in.|

## <a name="troubleshoot-iot-edge-errors"></a>Felsöka IoT Edge fel

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [felsöker problem med enhets aktivering](azure-stack-edge-gpu-troubleshoot-activation.md).
