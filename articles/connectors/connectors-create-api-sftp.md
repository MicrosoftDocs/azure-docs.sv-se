---
title: Anslut till SFTP-konto (inaktuellt)
description: Automatisera uppgifter och processer som övervakar, skapar, hanterar, skickar och tar emot filer för en SFTP-server med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673814"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer i Azure Logic Apps

> [!IMPORTANT]
> Använd [SFTP-ssh-anslutningen](../connectors/connectors-sftp-ssh.md) eftersom SFTP-anslutningen är föråldrad. Du kan inte längre välja SFTP-utlösare och åtgärder i Logic Apps designer.

Om du vill automatisera aktiviteter som övervakar, skapar, skickar och tar emot filer på en [säker File Transfer Protocol-server (SFTP)](https://www.ssh.com/ssh/sftp/) kan du skapa och automatisera integrerings arbets flöden med Azure Logic Apps och SFTP-anslutningen. SFTP är ett nätverksprotokoll som ger filåtkomst, filöverföring och filhantering via valfri betrodd dataström. Här följer några exempel på uppgifter som du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista och ta bort filer.
* Hämta fil innehåll och metadata.
* Extrahera arkiv till mappar.

Du kan använda utlösare som övervakar händelser på din SFTP-server och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också använda andra åtgärder i din Logic-app för att använda utdata från SFTP-åtgärder. Om du till exempel regelbundet hämtar filer från din SFTP-server kan du skicka e-postaviseringar om filerna och deras innehåll med hjälp av Office 365 Outlook Connector eller Outlook.com Connector. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Gränser

SFTP-anslutaren hanterar endast filer som är *50 MB eller mindre* och stöder inte [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). För större filer använder du [SFTP-SSH-anslutningsprogrammet](../connectors/connectors-sftp-ssh.md). Om du vill ha skillnader mellan SFTP-anslutningen och SFTP-SSH-anslutningen kan du läsa [jämföra SFTP – SSH jämfört med SFTP](../connectors/connectors-sftp-ssh.md#comparison) i SFTP-SSH-artikeln.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Dina SFTP-server-och kontoautentiseringsuppgifter som låter din Logic app komma åt ditt SFTP-konto. Om du vill använda [SSH-protokollet (Secure Shell)](https://www.ssh.com/ssh/protocol/) måste du också ha åtkomst till en privat SSH-nyckel och lösen ordet för den privata SSH-nyckeln.

  > [!NOTE]
  >
  > SFTP-anslutningen stöder de här privata nyckel formaten: OpenSSH, ssh.com och SparaTillFil
  >
  > När du skapar din Logic app måste du ange anslutnings information för din SFTP-server när du har lagt till den SFTP-utlösare eller åtgärd du vill ha. 
  > Om du använder en privat SSH-nyckel, se till att du ***kopierar*** nyckeln från filen med din privata SSH-nyckel och ***klistrar in*** nyckeln i anslutnings informationen, ***Ange eller redigera inte nyckeln manuellt***, vilket kan leda till att anslutningen Miss klaras. 
  > Mer information finns i de senare stegen i den här artikeln.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt SFTP-konto. [Skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att starta med en SFTP-utlösare. Om du vill använda en SFTP-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="how-sftp-triggers-work"></a>Så här fungerar SFTP-utlösare

SFTP-utlösare fungerar genom att söka i SFTP-filsystemet och leta efter en fil som har ändrats sedan den senaste avsökningen. Med vissa verktyg kan du bevara tidsstämpeln när filerna ändras. I dessa fall måste du inaktivera den här funktionen så att utlösaren kan fungera. Här följer några vanliga inställningar:

| SFTP-klient | Action |
|-------------|--------|
| WinSCP | Gå till **alternativ**  >  **Inställningar**  >  **överför**  >  **Redigera**  >  **bevara tidsstämpel**  >  **inaktivera** |
| FileZilla | Gå till **överför**  >  **bevara tidsstämplar för överförda filer**  >  **inaktivera** |
|||

När en utlösare hittar en ny fil, kontrollerar utlösaren att den nya filen är fullständig och inte delvis skriven. En fil kan till exempel ha ändringar som pågår när utlösaren kontrollerar fil servern. För att undvika att returnera en delvis skriven fil, noterar utlösaren tidsstämpeln för filen som har nyligen gjorda ändringar, men returnerar inte omedelbart den filen. Utlösaren returnerar filen endast när servern avsöks igen. Ibland kan det här problemet orsaka en fördröjning som är upp till två gånger utlösaren för avsöknings intervall.

## <a name="connect-to-sftp"></a>Anslut till SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par anger du "SFTP" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare.

   \- eller -

   För befintliga Logic Apps, under det sista steget där du vill lägga till en åtgärd, väljer du **nytt steg**. I rutan Sök anger du "SFTP" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange nödvändig information för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger den privata SSH-nyckeln i egenskapen för den **privata SSH-nyckeln** följer du dessa ytterligare steg, som hjälper dig att ange det fullständiga och korrekta värdet för den här egenskapen. 
   > En ogiltig nyckel gör att anslutningen Miss fungerar.

   Även om du kan använda en text redigerare kan du följa exempel steg som visar hur du kopierar och klistrar in nyckeln på rätt sätt med hjälp av Notepad.exe som exempel.

   1. Öppna din privata SSH-nyckel fil i en text redigerare. I dessa steg används anteckningar som exempel.

   1. I **redigerings** menyn i Anteckningar väljer du **Markera alla**.

   1. Välj **Redigera**  >  **kopia**.

   1. I en SFTP-utlösare eller åtgärd som du har lagt till klistrar du in den *fullständiga* nyckeln som du kopierade i egenskapen **SSH Private Key** , som stöder flera rader. **_Se till att klistra in_*_ Key. _*_Ange eller redigera inte nyckeln manuellt_**.

1. När du är klar med att ange anslutnings informationen väljer du **skapa**.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar ett Logic app-arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar filens innehåll och hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-servern.

**Enterprise-exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kund order. Du kan sedan använda en SFTP-åtgärd, till exempel **Hämta fil innehåll** , så att du kan hämta Beställningens innehåll för ytterligare bearbetning och lagra den i en order databas.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Du kan till exempel lägga till utlösaren från föregående exempel och ett villkor som filens innehåll måste uppfylla. Om villkoret är sant kan åtgärden som hämtar innehållet köras.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](../data-factory/connector-sftp.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)