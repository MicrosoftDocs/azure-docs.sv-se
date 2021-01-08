---
title: Ansluta till SFTP-servern med SSH
description: Automatisera aktiviteter som övervakar, skapar, hanterar, skickar och tar emot filer för en SFTP-server med hjälp av SSH och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 388d747da692160ab6d0a89c0c35de348d921486
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016770"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av SSH och Azure Logic Apps

Om du vill automatisera aktiviteter som övervakar, skapar, skickar och tar emot filer på en [säker File Transfer Protocol-server (SFTP)](https://www.ssh.com/ssh/sftp/) med hjälp av SSH-protokollet [(Secure Shell)](https://www.ssh.com/ssh/protocol/) kan du skapa och automatisera integrerings arbets flöden med hjälp av Azure Logic Apps och SFTP-SSH-anslutaren. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström.

Här följer några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn på, uppdatera, lista och ta bort filer.
* Skapa mappar.
* Hämta fil innehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som övervakar händelser på din SFTP-server och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också använda andra åtgärder i din Logic-app för att använda utdata från SFTP-åtgärder. Om du till exempel regelbundet hämtar filer från din SFTP-server kan du skicka e-postaviseringar om filerna och deras innehåll med hjälp av Office 365 Outlook Connector eller Outlook.com Connector. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Mer skillnader mellan SFTP-SSH-anslutningsprogrammet och SFTP-anslutningen finns i avsnittet [jämföra SFTP – SSH och SFTP](#comparison) senare i det här avsnittet.

## <a name="limits"></a>Gränser

* SFTP-SSH-anslutningen har för närvarande inte stöd för dessa SFTP-servrar:

  * IBM-DataPower
  * MessageWay
  * Säker MFT för OpenText
  * GXS för OpenText

* SFTP-SSH-anslutaren stöder antingen autentisering med privat nyckel eller lösenordsautentisering, inte båda.

* SFTP – SSH-åtgärder som stöder [segment](../logic-apps/logic-apps-handle-large-messages.md) hantering kan hantera filer på upp till 1 GB, medan SFTP-SSH-åtgärder som inte stöder segment hantering kan hantera filer upp till 50 MB. Standard segment storleken är 15 MB, men den här storleken kan ändras dynamiskt, från och med 5 MB och ökade gradvis till 50 MB, baserat på faktorer som nätverks svars tid, Server svars tid och så vidare.

  > [!NOTE]
  > För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)kräver den här anslutningens ISE-märkta version segment för att använda [meddelande gränserna för ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

  Du kan åsidosätta det här anpassade beteendet när du [anger en konstant segment storlek](#change-chunk-size) som ska användas i stället. Den här storleken kan vara mellan 5 och 50 MB. Anta till exempel att du har en 45 MB-fil och ett nätverk som har stöd för fil storlek utan svars tid. Anpassningsbar segment resultat i flera anrop, i stället för ett anrop. Om du vill minska antalet anrop kan du prova att ange en segment storlek på 50 MB. Om din Logi Kap par tids gräns inträffar i olika scenarier, till exempel när du använder 15 MB-segment, kan du försöka minska storleken till 5 MB.

  Segment storleken är associerad med en anslutning, vilket innebär att du kan använda samma anslutning för åtgärder som stöder segment och sedan för åtgärder som inte stöder segment koppling. I det här fallet är segment storleken för åtgärder som inte stöder segment intervall mellan 5 MB och 50 MB. Den här tabellen visar vilka SFTP-SSH-åtgärder som stöder segment:

  | Åtgärd | Segment stöd | Åsidosätt stöd för segment storlek |
  |--------|------------------|-----------------------------|
  | **Kopiera fil** | Inga | Inte tillämpligt |
  | **Skapa fil** | Ja | Ja |
  | **Skapa mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Ta bort panel** | Inte tillämpligt | Inte tillämpligt |
  | **Extrahera arkiv till mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta fil innehåll** | Ja | Ja |
  | **Hämta fil innehåll med hjälp av sökväg** | Ja | Ja |
  | **Hämta filens metadata** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta metadata för fil med hjälp av sökväg** | Inte tillämpligt | Inte tillämpligt |
  | **Lista filer i mappen** | Inte tillämpligt | Inte tillämpligt |
  | **Byt namn på fil** | Inte tillämpligt | Inte tillämpligt |
  | **Uppdatera fil** | Inga | Inte tillämpligt |
  ||||

* SFTP – SSH-utlösare stöder inte meddelande segment. När du begär fil innehåll väljer utlösare endast filer som är 15 MB eller mindre. Om du vill hämta filer som är större än 15 MB följer du detta mönster i stället:

  1. Använd en SFTP-SSH-utlösare som bara returnerar fil egenskaper, till exempel **när en fil läggs till eller ändras (endast egenskaper)**.

  1. Följ utlösaren med åtgärden SFTP-SSH **Get File Content** , som läser den fullständiga filen och använder implicit meddelande segment.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Jämför SFTP – SSH kontra SFTP

Här följer några andra viktiga skillnader mellan SFTP-SSH-anslutningen och SFTP-anslutningen där SFTP-SSH-anslutningen har dessa funktioner:

* Använder [SSH.net-biblioteket](https://github.com/sshnet/SSH.NET), som är ett SSH-bibliotek med öppen källkod som stöder .net.

* Tillhandahåller åtgärden **Skapa mapp** , som skapar en mapp på den angivna sökvägen på SFTP-servern.

* Innehåller åtgärden **Byt namn på fil** , som byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till SFTP-servern *i upp till 1 timme*, vilket förbättrar prestandan och minskar antalet försök att ansluta till servern. Om du vill ställa in varaktigheten för den här funktionen för cachelagring redigerar du egenskapen [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) i SSH-konfigurationen på din SFTP-server.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Dina SFTP-server-och kontoautentiseringsuppgifter som låter din Logic app komma åt ditt SFTP-konto. Du måste också ha åtkomst till en privat SSH-nyckel och lösen ordet för den privata SSH-nyckeln. Om du vill använda segment vid överföring av stora filer behöver du både Läs-och Skriv behörighet för rotmappen på din SFTP-server. Annars får du fel meddelandet "401 obehörig".

  > [!IMPORTANT]
  >
  > SFTP-SSH-anslutaren stöder *endast* dessa format för privata nycklar, algoritmer och finger avtryck:
  >
  > * **Privata nyckel format**: RSA (Rivest Shamir Adleman) och DSA-nycklar (Digital Signature Algorithm) i både OpenSSH-och SSH.com-format. Om din privata nyckel är i fil formatet SparaTillFil (. PPK), måste [du först konvertera nyckeln till fil formatet openssh (. pem)](#convert-to-openssh).
  >
  > * **Krypteringsalgoritmer**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, AES-128-CBC, AES-192-CBC och aes-256-CBC
  >
  > * **Finger avtryck**: MD5
  >
  > När du har lagt till den SFTP-SSH-utlösare eller åtgärd som du vill använda i din Logic-app måste du ange anslutnings information för din SFTP-server. När du anger din privata SSH-nyckel för den här anslutningen kan du *_inte ange eller redigera nyckeln _ manuellt_*, vilket kan leda till att anslutningen Miss fungerar. Kontrol lera i stället att du _*_kopierar nyckeln_*_ från filen med din privata SSH-nyckel och _*_klistrar in_*_ nyckeln i anslutnings informationen. 
  > Mer information finns i avsnittet [ansluta till SFTP med SSH](#connect) senare i den här artikeln.

_ Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt SFTP-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta med en SFTP-SSH-utlösare. Om du vill använda en SFTP-SSH-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="how-sftp-ssh-triggers-work"></a>Så här fungerar SFTP-SSH-utlösare

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Avsöknings beteende

SFTP – SSH-utlösare avsöker SFTP-filsystemet och letar efter alla filer som har ändrats sedan den senaste avsökningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här följer några vanliga inställningar:

| SFTP-klient | Åtgärd |
|-------------|--------|
| WinSCP | Gå till **alternativ**  >  **Inställningar**  >  **överför**  >  **Redigera**  >  **bevara tidsstämpel**  >  **inaktivera** |
| FileZilla | Gå till **överför**  >  **bevara tidsstämplar för överförda filer**  >  **inaktivera** |
|||

När en utlösare hittar en ny fil, kontrollerar utlösaren att den nya filen är fullständig och inte delvis skriven. En fil kan till exempel ha ändringar som pågår när utlösaren kontrollerar fil servern. För att undvika att returnera en delvis skriven fil, noterar utlösaren tidsstämpeln för filen som har nyligen gjorda ändringar, men returnerar inte omedelbart den filen. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan det här problemet orsaka en fördröjning som är upp till två gånger utlösaren för avsöknings intervall.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Utlös upprepnings Skift och avvikelse

Anslutningsbaserade utlösare där du måste skapa en anslutning först, till exempel SFTP-SSH-utlösaren, skiljer sig från inbyggda utlösare som körs internt i Azure Logic Apps, till exempel [upprepnings utlösaren](../connectors/connectors-native-recurrence.md). I återkommande anslutningsbaserade utlösare är det återkommande schemat inte den enda driv rutin som styr körningen och tids zonen bestämmer den första start tiden. Efterföljande körningar är beroende av upprepnings schema, den senaste utlösnings körningen *och* andra faktorer som kan orsaka körnings tider eller orsaka oväntade beteende, till exempel att inte behålla det angivna schemat när sommar tid börjar och slutar. För att se till att upprepnings tiden inte skiftar när sommar tiden börjar gälla kan du justera upprepningen manuellt så att din Logic app fortsätter att köras vid den förväntade tiden. Annars flyttas start tiden en timme framåt när sommar tiden startar och en timme bakåt när sommar tiden slutar. Mer information finns i [upprepning för anslutningsbaserade utlösare](../connectors/apis-list.md#recurrence-connection-based).

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konvertera den SparaTillFil-baserade nyckeln till OpenSSH

Om den privata nyckeln är i formatet SparaTillFil, som använder fil namns tillägget. PPK (SparaTillFil-privat nyckel), måste du först konvertera nyckeln till OpenSSH-formatet, som använder fil namns tillägget. pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>UNIX-baserat OS

1. Om du inte har de SparaTillFil-verktyg som är installerade på systemet, gör du det nu, till exempel:

   `sudo apt-get install -y putty`

1. Kör det här kommandot, som skapar en fil som du kan använda med SFTP-SSH-anslutningen:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Ett exempel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Om du inte redan har gjort det kan du [Hämta det senaste verktyget SparaTillFil-generatorn (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)och sedan starta verktyget.

1. Välj **load** på den här skärmen.

   ![Välj load (Load)](./media/connectors-sftp-ssh/puttygen-load.png)

1. Bläddra till din privata nyckel fil i formatet SparaTillFil och välj **Öppna**.

1. Från menyn **konverteringar** väljer du **Exportera openssh nyckel**.

   ![Välj "Exportera OpenSSH key"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Spara den privata nyckel filen med `.pem` fil namns tillägget.

## <a name="considerations"></a>Överväganden

I det här avsnittet beskrivs vad du bör tänka på vid granskningen av kopplingens utlösare och åtgärder.

<a name="create-file"></a>

### <a name="create-file"></a>Skapa fil

Om du vill skapa en fil på din SFTP-server kan du använda åtgärden SFTP-SSH **create File** . När den här åtgärden skapar filen anropar Logic Apps-tjänsten också din SFTP-server automatiskt för att hämta filens metadata. Men om du flyttar den nyligen skapade filen innan Logic Appss tjänsten kan ringa för att hämta metadata visas ett `404` fel meddelande `'A reference was made to a file or folder which does not exist'` . Om du vill hoppa över att läsa filens metadata efter att filen har skapats följer du stegen för att [lägga till och ange egenskapen **Hämta alla fil-metadata** till **Nej**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Ansluta till SFTP med SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logic Apps, i sökrutan, anger du `sftp ssh` som filter. Välj den utlösare som du vill använda under listan utlösare.

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**. I rutan Sök anger `sftp ssh` du som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange nödvändig information för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger den privata SSH-nyckeln i egenskapen för den **privata SSH-nyckeln** följer du dessa ytterligare steg, som hjälper dig att ange det fullständiga och korrekta värdet för den här egenskapen. En ogiltig nyckel gör att anslutningen Miss fungerar.

   Även om du kan använda en text redigerare kan du följa exempel steg som visar hur du kopierar och klistrar in nyckeln på rätt sätt med hjälp av Notepad.exe som exempel.

   1. Öppna din privata SSH-nyckel fil i en text redigerare. I dessa steg används anteckningar som exempel.

   1. I **redigerings** menyn i Anteckningar väljer du **Markera alla**.

   1. Välj **Redigera**  >  **kopia**.

   1. I SFTP-SSH-utlösare eller åtgärd som du har lagt till klistrar du in den *fullständiga* nyckeln som du kopierade i egenskapen **SSH Private Key** , som stöder flera rader.  **_Se till att klistra in_* _ Key. _*_Ange eller redigera inte nyckeln manuellt_*_.

1. När du har angett anslutnings informationen väljer du _ * skapa * *.

1. Ange den information som krävs för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic app-arbetsflöde.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Åsidosätt segment storlek

Om du vill åsidosätta standard beteendet som segmenterar använder kan du ange en konstant segment storlek mellan 5 och 50 MB.

1. I åtgärdens övre högra hörn väljer du knappen ovaler (**...**) och väljer sedan **Inställningar**.

   ![Öppna SFTP – SSH-inställningar](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Under **innehålls överföring**, i egenskapen **segment storlek** , anger du ett heltals värde från `5` till `50` , till exempel: 

   ![Ange den segment storlek som ska användas i stället](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. När du är klar väljer du **klar**.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar ett Logic app-arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-servern.

**Enterprise-exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kund order. Du kan sedan använda en SFTP-åtgärd, till exempel **Hämta fil innehåll** , så att du kan hämta Beställningens innehåll för ytterligare bearbetning och lagra den i en order databas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP – SSH-åtgärd: Hämta fil innehåll med hjälp av sökväg

Den här åtgärden hämtar innehållet från en fil på en SFTP-server genom att ange sökvägen till filen. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Felsöka problem

I det här avsnittet beskrivs möjliga lösningar på vanliga fel eller problem.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 fel: "ett anslutnings försök misslyckades på grund av att den anslutna parten inte svarade korrekt efter en tids period, eller upprättad anslutning misslyckades eftersom den anslutna värden inte svarade" eller "begäran till SFTP-servern tog mer än 00:00:30 sekunder"

Det här felet kan inträffa när din Logic app inte kan upprätta en anslutning till SFTP-servern. Det kan finnas olika orsaker till det här problemet, så prova följande fel söknings alternativ:

* Tids gränsen för anslutningen är 20 sekunder. Kontrol lera att SFTP-servern har goda prestanda-och mellanliggande enheter, t. ex. brand väggar, inte lägger till resurser. 

* Om du har konfigurerat en brand vägg kontrollerar du att du lägger till **IP-adresser för hanterade anslutningar** i listan över godkända. För att hitta IP-adresserna för din Logic Apps region, se [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Om det här felet inträffar intermittent ändrar du princip inställningen för att **försöka igen** för SFTP-SSH-åtgärden till ett antal försök som är högre än de fyra standard Återförsöken.

* Kontrol lera om SFTP-servern anger en gräns för antalet anslutningar från varje IP-adress. Om det finns en gräns kan du behöva begränsa antalet samtidiga Logic App-instanser.

* För att minska kostnaderna för anslutnings etablering, i SSH-konfigurationen för din SFTP-server, ökar du egenskapen [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) till cirka en timme.

* Granska SFTP-serverloggen för att kontrol lera om begäran från Logic app nådde SFTP-servern. Om du vill ha mer information om anslutnings problemet kan du också köra ett nätverks spår på din brand vägg och din SFTP-server.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 fel: "en referens gjordes till en fil eller mapp som inte finns"

Det här felet kan inträffa när din Logi Kap par skapar en ny fil på din SFTP-server genom åtgärden SFTP-SSH **create File** , men den nyligen skapade filen flyttas direkt innan Logic Apps tjänsten kan hämta filens metadata. När din Logi Kap par kör åtgärden för att **Skapa fil** anropar Logic Apps-tjänsten också din SFTP-server automatiskt för att hämta filens metadata. Men om din Logic-app flyttar filen kan Logic Apps-tjänsten inte längre hitta filen så att du får `404` fel meddelandet.

Om du inte kan undvika eller försena flyttningen av filen kan du hoppa över att läsa filens metadata när du har skapat filen i stället för att följa dessa steg:

1. I åtgärden **Skapa fil** öppnar du listan **Lägg till ny parameter** , väljer egenskapen **Hämta alla fil-metadata** och anger värdet till **Nej**.

1. Om du behöver dessa fil-metadata senare kan du använda åtgärden **Hämta filens metadata** .

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/sftpwithssh/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)måste den här anslutningens ISE-märkta version innehålla segment för att använda [meddelande gränserna för ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
