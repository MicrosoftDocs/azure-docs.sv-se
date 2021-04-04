---
title: Anslut till fil system lokalt
description: Automatisera aktiviteter och arbets flöden som ansluter till lokala fil system med fil system anslutningen via den lokala datagatewayen i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91873242"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ansluta till lokala filsystem med Azure Logic Apps

Med Azure Logic Apps och fil system anslutningen kan du skapa automatiserade uppgifter och arbets flöden som skapar och hanterar filer på en lokal fil resurs, till exempel:

- Skapa, Hämta, lägga till, uppdatera och ta bort filer.
- Visa filer i mappar eller rotmappar.
- Hämta fil innehåll och metadata.

  > [!IMPORTANT]
  > Fil system anslutningen har för närvarande endast stöd för Windows fil system på Windows-operativsystem.  

Den här artikeln visar hur du kan ansluta till ett lokalt fil system enligt beskrivningen i det här exempel scenariot: kopiera en fil som laddas upp till Dropbox till en fil resurs och skicka sedan ett e-postmeddelande. För att på ett säkert sätt ansluta och komma åt lokala system använder Logic Apps den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Information om anslutningsspecifika teknisk information finns i referens för [fil system anslutning](/connectors/filesystem/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Innan du kan ansluta Logi Kap par till lokala system, till exempel fil system servern, måste du [Installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange att din gateway-installation ska användas när du skapar fil system anslutningen från din Logic app.

* Ett [Dropbox-konto](https://www.dropbox.com/)som du kan registrera dig kostnads fritt. Dina kontoautentiseringsuppgifter krävs för att skapa en anslutning mellan din Logic app och ditt Dropbox-konto.

* Åtkomst till den dator som har det fil system som du vill använda. Om du till exempel installerar datagatewayen på samma dator som fil systemet behöver du kontots autentiseringsuppgifter för den datorn.

* Ett e-postkonto från en provider som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). Den här Logic-appen använder ett arbets-eller skol konto. Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något.

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom Logic-app.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Skriv "Dropbox" som filter i rutan Sök. Välj den här utlösaren i listan utlösare: **när en fil skapas**

   ![Välj Dropbox-utlösare](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Logga in med dina autentiseringsuppgifter för Dropbox-kontot och ge åtkomst till Dropbox-data för Azure Logic Apps.

1. Ange den information som krävs för utlösaren.

   ![Dropbox-utlösare](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Lägg till åtgärder

1. Under utlösaren väljer du **Nästa steg**. I rutan Sök anger du "fil system" som filter. I listan åtgärder väljer du den här åtgärden: **Skapa fil**

   ![Hitta fil Systems koppling](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Om du inte redan har en anslutning till fil systemet uppmanas du att skapa en anslutning.

   ![Skapa anslutning](media/logic-apps-using-file-connector/file-system-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Anslutnings namn** | Ja | <*anslutnings namn*> | Det namn som du vill använda för anslutningen |
   | **Rotmapp** | Ja | <*rot-mapp-namn*> | Rotmappen för ditt fil system, till exempel om du har installerat din lokala datagateway, till exempel en lokal mapp på datorn där den lokala datagatewayen är installerad, eller mappen för en nätverks resurs som datorn har åtkomst till. <p>Exempelvis: `\\PublicShare\\DropboxFiles` <p>Rotmappen är den överordnade huvudmappen som används för relativa sökvägar för alla filrelaterade åtgärder. |
   | **Autentiseringstyp** | Inga | <*typ av autentisering*> | Den typ av autentisering som fil systemet använder: **Windows** |
   | **Användarnamn** | Ja | < > \\ domän < *användar namn*> <p>\- eller - <p><*lokal* > \\ dator < *användar namn*> | Användar namnet för den dator där du har fil systemets mapp. <p>Om din fil system katalog finns på samma dator som den lokala datagatewayen kan du använda <användar namn för *lokal dator* > \\ < >. |
   | **Lösenord** | Ja | <*ditt lösen ord*> | Lösen ordet för datorn där du har fil systemet |
   | **nyckeln** | Ja | <*installerat – Gateway-namn*> | Namnet på din tidigare installerade Gateway |
   |||||

1. När du är klar väljer du **Skapa**.

   Logic Apps konfigurerar och testar anslutningen och kontrollerar att anslutningen fungerar korrekt. Om anslutningen är korrekt konfigurerad visas alternativ för den åtgärd som du tidigare har valt.

1. I åtgärden **Skapa fil** anger du information om hur du kopierar filer från Dropbox till rotmappen i din lokala fil resurs. Om du vill lägga till utdata från föregående steg klickar du i rutorna och väljer från tillgängliga fält när listan med dynamiskt innehåll visas.

   ![Åtgärd för att skapa fil](media/logic-apps-using-file-connector/create-file-filled.png)

1. Nu ska du lägga till en Outlook-åtgärd som skickar ett e-postmeddelande så att rätt användare vet om den nya filen. Ange mottagare, titel och brödtext för e-postmeddelandet. För testning kan du använda din egen e-postadress.

   ![Åtgärden Skicka ett e-postmeddelande](media/logic-apps-using-file-connector/send-email.png)

1. Spara logikappen. Testa din app genom att ladda upp en fil till Dropbox.

   Din Logic app ska kopiera filen till din lokala fil resurs och skicka mottagarna ett e-postmeddelande om den kopierade filen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/fileconnector/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter till lokala data](../logic-apps/logic-apps-gateway-connection.md) 
* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
