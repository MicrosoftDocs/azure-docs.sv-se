---
title: Ansluta till SFTP-server med SSH
description: Automatisera uppgifter som övervakar, skapar, hanterar, skickar och tar emot filer för en SFTP-server med hjälp av SSH och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781567"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>Skapa och hantera SFTP-filer med SSH och Azure Logic Apps

Om du vill automatisera uppgifter som skapar och hanterar filer på en [Secure File Transfer Protocol-server (SFTP)](https://www.ssh.com/ssh/sftp/) med hjälp av [SSH-protokollet (Secure Shell)](https://www.ssh.com/ssh/protocol/) kan du skapa automatiserade integreringsarbetsflöden med hjälp av Azure Logic Apps och SFTP-SSH-anslutningsappen. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström.

Här är några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn, uppdatera, lista och ta bort filer.
* Skapa mappar.
* Hämta filinnehåll och metadata.
* Extrahera arkiv till mappar.

I arbetsflödet kan du använda en utlösare som övervakar händelser på SFTP-servern och gör utdata tillgängliga för andra åtgärder. Du kan sedan använda åtgärder för att utföra olika uppgifter på SFTP-servern. Du kan också inkludera andra åtgärder som använder utdata från SFTP-SSH-åtgärder. Om du till exempel regelbundet hämtar filer från SFTP-servern kan du skicka e-postaviseringar om dessa filer och deras innehåll med hjälp av Office 365 Outlook-anslutningsappen eller Outlook.com anslutningsappen. Om logikappar är nytt för dig kan du läsa [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Information om skillnaderna mellan SFTP-SSH-anslutningsappen och SFTP-anslutningsappen finns i avsnittet Jämför [SFTP-SSH](#comparison) jämfört med SFTP senare i det här avsnittet.

## <a name="limits"></a>Gränser

* SFTP-SSH-anslutningsappen stöder för närvarande inte dessa SFTP-servrar:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* SFTP-SSH-åtgärder [](../logic-apps/logic-apps-handle-large-messages.md) som stöder segmentering kan hantera filer upp till 1 GB, medan SFTP-SSH-åtgärder som inte stöder segmentering kan hantera filer upp till 50 MB. Standardstorleken för segment är 15 MB. Den här storleken kan dock ändras dynamiskt, från 5 MB och gradvis öka till maximalt 50 MB. Dynamisk storlek baseras på faktorer som nätverksfördröjning, serverns svarstid och så vidare.

  > [!NOTE]
  > För logikappar i en [Integration Service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)kräver den här anslutningsappens ISE-märkta version segmentering för att [använda ISE-meddelandebegränsningarna i](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) stället.

  Du kan åsidosätta det här anpassningsbara beteendet när [du anger en konstant segmentstorlek som](#change-chunk-size) ska användas i stället. Den här storleken kan vara mellan 5 MB och 50 MB. Anta till exempel att du har en fil på 45 MB och ett nätverk som har stöd för den filstorleken utan fördröjning. Anpassningsbar segmentering resulterar i flera anrop, i stället för det enda anropet. Om du vill minska antalet anrop kan du prova att ange en segmentstorlek på 50 MB. I ett annat scenario kan du försöka att minska storleken till 5 MB om din logikapp till exempel använder segment på 15 MB.

  Segmentstorleken är associerad med en anslutning. Det här attributet innebär att du kan använda samma anslutning för båda åtgärderna som stöder segmentering och åtgärder som inte stöder segmentering. I det här fallet sträcker sig segmentstorleken för åtgärder som inte stöder segmentering från 5 MB till 50 MB. Den här tabellen visar vilka SFTP-SSH-åtgärder som stöder segmentering:

  | Action | Stöd för segmentering | Åsidosätt stöd för segmentstorlek |
  |--------|------------------|-----------------------------|
  | **Kopiera fil** | No | Inte tillämpligt |
  | **Skapa fil** | Ja | Ja |
  | **Skapa mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Ta bort fil** | Inte tillämpligt | Inte tillämpligt |
  | **Extrahera arkiv till mapp** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta filinnehåll** | Ja | Ja |
  | **Hämta filinnehåll med hjälp av sökväg** | Ja | Ja |
  | **Hämta filmetadata** | Inte tillämpligt | Inte tillämpligt |
  | **Hämta filmetadata med sökväg** | Inte tillämpligt | Inte tillämpligt |
  | **Visa en lista över filer i mappen** | Inte tillämpligt | Inte tillämpligt |
  | **Byt namn på fil** | Inte tillämpligt | Inte tillämpligt |
  | **Uppdatera fil** | No | Inte tillämpligt |
  ||||

* SFTP-SSH-utlösare stöder inte segmentering av meddelanden. När du begär filinnehåll väljer utlösare endast filer som är 15 MB eller mindre. Om du vill hämta filer som är större än 15 MB följer du det här mönstret i stället:

  1. Använd en SFTP-SSH-utlösare som endast returnerar filegenskaper. Dessa utlösare har namn som innehåller beskrivningen **(endast egenskaper).**

  1. Följ utlösaren med åtgärden SFTP-SSH **Hämta filinnehåll.** Den här åtgärden läser den fullständiga filen och använder implicit meddelandedelning.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Jämför SFTP-SSH jämfört med SFTP

I följande lista beskrivs viktiga SFTP-SSH-funktioner som skiljer sig från SFTP-anslutningsappen:

* Använder SSH.NET [,](https://github.com/sshnet/SSH.NET)som är ett SSH-bibliotek (Secure Shell) med öppen källkod som stöder .NET.

* Tillhandahåller åtgärden **Skapa mapp,** som skapar en mapp på den angivna sökvägen på SFTP-servern.

* Tillhandahåller åtgärden **Byt namn** på fil, som byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till *SFTP-servern i upp till 1 timme.* Den här funktionen förbättrar prestanda och minskar hur ofta anslutningsappen försöker ansluta till servern. Om du vill ange varaktigheten för det här cachelagringsbeteendet redigerar du egenskapen [ **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) i SSH-konfigurationen på SFTP-servern.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din SFTP-serveradress och autentiseringsuppgifter för kontot, så att arbetsflödet kan komma åt ditt SFTP-konto. Du behöver också åtkomst till en privat SSH-nyckel och lösenordet för den privata SSH-nyckeln. Om du vill ladda upp stora filer med segmentering behöver du både läs- och skrivåtkomst för rotmappen på SFTP-servern. Annars får du felet "401 Obehörig".

  SFTP-SSH-anslutningsappen stöder både autentisering med privat nyckel och lösenordsautentisering. SFTP-SSH-anslutningsappen stöder *dock endast* dessa privata nyckelformat, algoritmer och fingeravtryck:

  * **Privata nyckelformat:** RSA-nycklar (Rivest Shamir Adleman) och DSA-nycklar (digital signaturalgoritm) i både OpenSSH- och ssh.com format. Om din privata nyckel är i PuTTY-filformat (.ppk) konverterar du först nyckeln till [OpenSSH-filformatet (.pem).](#convert-to-openssh)
  * **Krypteringsalgoritmer:** DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC och AES-256-CBC
  * **Fingeravtryck:** MD5

  När du har lagt till en SFTP-SSH-utlösare eller -åtgärd i arbetsflödet måste du ange anslutningsinformation för SFTP-servern. När du anger din privata SSH-nyckel för den här anslutningen **ska*** inte manuellt ange eller redigera nyckeln _, vilket kan orsaka att anslutningen misslyckas. Se i stället till att _*_kopiera nyckeln_*_ från din privata SSH-nyckelfil och _ *_klistra_* in * nyckeln i anslutningsinformationen. Mer information finns i avsnittet [Ansluta till SFTP med SSH senare](#connect) i den här artikeln.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappens arbetsflöde där du vill komma åt ditt SFTP-konto. Börja med en SFTP-SSH-utlösare genom att [skapa ett tomt logikapparbetsflöde](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-SSH-åtgärd startar du arbetsflödet med en annan utlösare, till exempel **upprepningsutlösaren.**

## <a name="how-sftp-ssh-triggers-work"></a>Så här fungerar SFTP-SSH-utlösare

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Avsökningsbeteende

SFTP-SSH-utlösare avsöker SFTP-filsystemet och letar efter filer som har ändrats sedan den senaste avsökning. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här är några vanliga inställningar:

| SFTP-klient | Action |
|-------------|--------|
| Winscp | Gå till  >  **Alternativinställningar**  >  **Överför Redigera**  >  **Bevara**  >  **tidsstämpel**  >  **Inaktivera** |
| Filezilla | Gå till **Överför Bevara**  >  **tidsstämplar för överförda filer**  >  **Inaktivera** |
|||

När en utlösare hittar en ny fil kontrollerar utlösaren att den nya filen är klar och inte delvis skriven. En fil kan till exempel ha pågående ändringar när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriven fil antecknar utlösaren tidsstämpeln för filen som har de senaste ändringarna, men returnerar inte omedelbart filen. Utlösaren returnerar bara filen när servern avsöks igen. Ibland kan det här beteendet orsaka en fördröjning som är upp till dubbelt så stor som utlösarens avsökningsintervall.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Utlösa upprepningsförskjutning och förskjutning

Anslutningsbaserade utlösare där du först behöver skapa en anslutning, till exempel SFTP-SSH-utlösaren, skiljer sig från inbyggda [](../connectors/connectors-native-recurrence.md)utlösare som körs inbyggt i Azure Logic Apps, till exempel upprepningsutlösaren . I återkommande anslutningsbaserade utlösare är upprepningsschemat inte den enda drivrutinen som styr körningen, och tidszonen avgör bara den första starttiden. Efterföljande körningar beror på upprepningsschemat,  den senaste utlösarkörningen och andra faktorer som kan orsaka drifttidsavdrift eller generera oväntat beteende. Ett oväntat beteende kan till exempel innebära att det inte går att upprätthålla det angivna schemat när sommartid (DST) startar och slutar. Justera upprepningen manuellt för att se till att upprepningstiden inte ändras när DST har verkställts. På så sätt fortsätter arbetsflödet att köras vid den förväntade tiden. Annars flyttas starttiden en timme framåt när DST startar och en timme bakåt när DST avslutas. Mer information finns i [Upprepning för anslutningsbaserade utlösare.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konvertera PuTTY-baserad nyckel till OpenSSH

PuTTY-format och OpenSSH-format använder olika filnamnstillägg. PuTTY-formatet använder filnamnstillägget .ppk eller PuTTY Private Key. OpenSSH-formatet använder filnamnstillägget .pem Privacy Enhanced Mail eller ett annat filnamnstillägg. Om din privata nyckel är i PuTTY-format och du måste använda OpenSSH-format konverterar du först nyckeln till OpenSSH-formatet genom att följa dessa steg:

### <a name="unix-based-os"></a>Unix-baserat operativsystem

1. Om du inte har PuTTY-verktygen installerade i systemet gör du det nu, till exempel:

   `sudo apt-get install -y putty`

1. Kör det här kommandot, vilket skapar en fil som du kan använda med SFTP-SSH-anslutningsappen:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Exempel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Om du inte redan har gjort det laddar du ned det senaste [verktyget PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)och startar sedan verktyget.

1. På den här skärmen väljer du **Läs in.**

   ![Välj "Läs in"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Bläddra till filen med din privata nyckel i PuTTY-format och välj **Öppna**.

1. I menyn **Konverteringar** väljer du **Exportera OpenSSH-nyckel.**

   ![Välj "Exportera OpenSSH-nyckel"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Spara filen med den privata nyckeln `.pem` med filnamnstillägget.

## <a name="considerations"></a>Överväganden

Det här avsnittet beskriver överväganden att granska när du använder den här anslutningsappens utlösare och åtgärder.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Använda olika SFTP-mappar för filöverföring och bearbetning

På SFTP-servern använder du separata mappar för att lagra uppladdade filer och för utlösaren för att övervaka filerna för bearbetning. Annars utlöses inte utlösaren och beter sig oförutsägbart, till exempel hoppar över ett slumpmässigt antal filer som utlösaren bearbetar. Det här kravet innebär dock att du behöver ett sätt att flytta filer mellan dessa mappar. 

Om det här utlösarproblemet inträffar tar du bort filerna från mappen som utlösaren övervakar och använder en annan mapp för att lagra de uppladdade filerna.

<a name="create-file"></a>

### <a name="create-file"></a>Skapa fil

Om du vill skapa en fil på SFTP-servern  kan du använda åtgärden Skapa fil för SFTP-SSH. När den här åtgärden skapar filen anropar Logic Apps också automatiskt SFTP-servern för att hämta filens metadata. Men om du flyttar den nyligen skapade filen innan Logic Apps-tjänsten kan göra anropet för att hämta metadata får `404` du ett felmeddelande, `'A reference was made to a file or folder which does not exist'` . Om du vill hoppa över läsningen av filens metadata när filen har skapats följer du stegen för att lägga till och ange egenskapen Hämta [ **alla filmetadata** till **Nej.**](#file-does-not-exist)

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Ansluta till SFTP med SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du som filter `sftp ssh` i sökrutan. Under listan med utlösare väljer du den utlösare som du vill använda.

   \- eller -

   För befintliga logikappar väljer du Nytt steg under det sista steget där du vill lägga **till en åtgärd.** I sökrutan anger du `sftp ssh` som filter. Under åtgärdslistan väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan steg flyttar du pekaren över pilen mellan stegen. Välj plustecknet ( **+** ) som visas och välj sedan Lägg till en **åtgärd**.

1. Ange nödvändig information för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger din privata SSH-nyckel i egenskapen för den privata **SSH-nyckeln** följer du dessa ytterligare steg, som ser till att du anger det fullständiga och korrekta värdet för den här egenskapen. En ogiltig nyckel gör att anslutningen misslyckas.

   Även om du kan använda valfri textredigerare finns här exempelsteg som visar hur du kopierar och klistrar in nyckeln korrekt med hjälp av Notepad.exe som exempel.

   1. Öppna filen med din privata SSH-nyckel i en textredigerare. I de här stegen används Anteckningar som exempel.

   1. På menyn Redigera **i Anteckningar** väljer du **Välj alla.**

   1. Välj **Redigera**  >  **kopiera.**

   1. I SFTP-SSH-utlösaren  eller -åtgärden klistrar du in den fullständiga kopierade nyckeln i den privata **SSH-nyckelegenskapen,** som stöder flera rader. **_Ange eller redigera inte nyckeln manuellt._**

1. När du har angett anslutningsinformationen väljer du **Skapa**.

1. Ange nu nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Åsidosätt segmentstorlek

Om du vill åsidosätta det anpassningsbara standardbeteendet som segmentering använder kan du ange en konstant segmentstorlek från 5 MB till 50 MB.

1. I åtgärdens övre högra hörn väljer du ellipsknappen (**...**) och sedan **Inställningar.**

   ![Öppna SFTP-SSH-inställningar](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Under **Innehållsöverföring** i egenskapen **Segmentstorlek** anger du ett heltalsvärde `5` från till , till `50` exempel: 

   ![Ange segmentstorlek som ska användas i stället](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. När du är klar väljer du **Klar.**

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-utlösare: När en fil läggs till eller ändras

Den här utlösaren startar ett arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Som exempel på uppföljningsåtgärder kan arbetsflödet använda ett villkor för att kontrollera om filinnehållet uppfyller angivna villkor. Om innehållet uppfyller villkoret kan åtgärden **Hämta** filinnehåll SFTP-SSH hämta innehållet, och sedan kan en annan SFTP-SSH-åtgärd placera filen i en annan mapp på SFTP-servern.

**Företagsexempel:** Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-SSH-åtgärd, till exempel Hämta filinnehåll, så att du kan hämta orderns innehåll för vidare bearbetning och lagra beställningen i en orderdatabas. 

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP – SSH-åtgärd: Hämta filinnehåll med hjälp av sökväg

Den här åtgärden hämtar innehållet från en fil på en SFTP-server genom att ange filsökvägen. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Felsöka problem

I det här avsnittet beskrivs möjliga lösningar på vanliga fel eller problem.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504-fel: "Ett anslutningsförsök misslyckades på grund av att den anslutna parten inte svarade korrekt efter en viss tid eller upprättad anslutning misslyckades eftersom den anslutna värden inte svarade" eller "Begäran till SFTP-servern har tagit mer än "00:00:30" sekunder"

Det här felet kan inträffa när logikappen inte kan upprätta en anslutning till SFTP-servern. Det kan finnas olika orsaker till det här problemet, så prova följande felsökningsalternativ:

* Tidsgränsen för anslutningen är 20 sekunder. Kontrollera att SFTP-servern har bra prestanda och mellanliggande enheter, till exempel brandväggar, inte lägger till extra kostnader. 

* Om du har en brandvägg konfigurerad kontrollerar du att du lägger till IP-adresserna för den **hanterade** anslutningsappen i listan över godkända anslutningar. Information om hur du hittar IP-adresserna för logikappens region finns [i Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Om det här felet inträffar tillfälligt ändrar du inställningen Återförsöksprincip för SFTP-SSH-åtgärden till ett återförsöksantal som är högre än de fyra standardförsöken. 

* Kontrollera om SFTP-servern begränsar antalet anslutningar från varje IP-adress. Om det finns en gräns kan du behöva begränsa antalet samtidiga logikappinstanser.

* Minska kostnaden för anslutningsetablering genom att öka egenskapen [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) till ungefär en timme i SSH-konfigurationen för SFTP-servern.

* Granska SFTP-serverloggen för att kontrollera om begäran från logikappen nådde SFTP-servern. Om du vill ha mer information om anslutningsproblemet kan du även köra en nätverksspårning på brandväggen och SFTP-servern.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-fel: "En referens har gjorts till en fil eller mapp som inte finns"

Det här felet kan inträffa när arbetsflödet skapar en fil på  SFTP-servern med åtgärden skapa fil för SFTP-SSH, men omedelbart flyttar filen innan Logic Apps-tjänsten kan hämta filens metadata. När arbetsflödet kör **åtgärden Skapa fil** anropar Logic Apps automatiskt SFTP-servern för att hämta filens metadata. Men om logikappen flyttar filen kan Logic Apps inte längre hitta filen, så du får `404` felmeddelandet.

Om du inte kan undvika eller fördröja flytten av filen kan du hoppa över läsningen av filens metadata när filen har skapats i stället genom att följa dessa steg:

1. I åtgärden **Skapa fil** öppnar du listan Lägg till **ny parameter,** väljer egenskapen **Hämta alla filmetadata** och anger värdet till **Nej.**

1. Om du behöver dessa filmetadata senare kan du använda **åtgärden Hämta filmetadata.**

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningsappen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i anslutningsappens Swagger-fil, finns på [anslutningsappens referenssida.](/connectors/sftpwithssh/)

> [!NOTE]
> För logikappar i en [Integration Service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)kräver den här anslutningsappens ISE-märkta version segmentering för att [använda ISE-meddelandebegränsningarna i](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) stället.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Logic Apps anslutningsappar](../connectors/apis-list.md)
