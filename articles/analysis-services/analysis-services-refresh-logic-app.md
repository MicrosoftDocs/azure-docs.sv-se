---
title: Uppdatera med Logic Apps för Azure Analysis Servicess modeller | Microsoft Docs
description: Den här artikeln beskriver hur du kodar asynkron uppdatering för Azure Analysis Services med hjälp av Azure Logic Apps.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019094"
---
# <a name="refresh-with-logic-apps"></a>Uppdatera med Logic Apps

Genom att använda Logic Apps-och REST-anrop kan du utföra automatiserade data uppdaterings åtgärder på dina tabell modeller i Azure Analysis, inklusive synkronisering av skrivskyddade repliker för frågans skalbarhet.

Mer information om hur du använder REST-API: er med Azure Analysis Services finns i [asynkron uppdatering med REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory-token (OAuth 2).  I exemplen i den här artikeln används ett tjänst huvud namn (SPN) för att autentisera till Azure Analysis Services. Läs mer i [skapa ett tjänst huvud namn med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Utforma Logic app

> [!IMPORTANT]
> I följande exempel förutsätter vi att Azure Analysis Services brand väggen är inaktive rad. Om brand väggen är aktive rad måste den offentliga IP-adressen för begär ande initieraren läggas till i listan över godkända i Azure Analysis Services brand väggen. Mer information om Azure Logic Apps IP-adressintervall per region finns i [gränser och konfigurations information för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Förutsättningar

#### <a name="create-a-service-principal-spn"></a>Skapa ett huvud namn för tjänsten (SPN)

Information om hur du skapar ett huvud namn för tjänsten finns i [skapa ett tjänst huvud namn med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Tjänstens huvud namn som du skapar måste ha Server administratörs behörighet på servern. Mer information finns i [lägga till ett huvud namn för tjänsten i Server administratörs rollen](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurera Logic-appen

I det här exemplet är Logic app utformad för att utlösa när en HTTP-begäran tas emot. Detta gör att du kan använda ett Orchestration-verktyg, till exempel Azure Data Factory, för att utlösa uppdatering av Azure Analysis Servicess modellen.

När du har skapat en Logic app:

1. I Logic App Designer väljer du den första åtgärden som **när en HTTP-begäran tas emot**.

   ![Lägg till HTTP-mottagen aktivitet](./media/analysis-services-async-refresh-logic-app/1.png)

Det här steget fylls i med HTTP POST-URL: en när Logic-appen har sparats.

2. Lägg till ett nytt steg och Sök efter **http**.  

   ![Skärm bild av avsnittet "Välj en åtgärd" med panelen "HTTP" vald.](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Skärm bild av "HTTP"-fönstret med panelen "HTTP-HTTP" vald.](./media/analysis-services-async-refresh-logic-app/10.png)

3. Välj **http** för att lägga till den här åtgärden.

   ![Lägg till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurera HTTP-aktiviteten enligt följande:

|Egenskap  |Värde  |
|---------|---------|
|**Metod**     |POST         |
|**URI**     | https://*Your Server region*/servers/*AAS Server Name*/Models/*ditt databas namn*/refreshes <br /> <br /> Till exempel: https: \/ /westus.asazure.Windows.net/servers/myserver/Models/AdventureWorks/refreshes|
|**Sidhuvuden**     |   Innehålls typ, Application/JSON <br /> <br />  ![Sidhuvuden](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Brödtext**     |   Mer information om hur du skapar begär ande texten finns i [asynkron uppdatering med REST API-post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autentisering**     |Active Directory OAuth         |
|**Innehav**     |Fyll i din Azure Active Directory TenantId         |
|**Målgrupp**     |https://*., Azure. Windows. net         |
|**Klient-ID**     |Ange tjänstens huvud namn ClientID         |
|**Autentiseringstyp**     |Hemlighet         |
|**Icke**     |Ange hemligheten för tjänstens huvud namn         |

Exempel:

![Slutförd HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/7.png)

Testa nu Logic app.  I Logic App Designer klickar du på **Kör**.

![Testa logikappen](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Använda Logic-appen med Azure Data Factory

När Logic-appen har sparats granskar du aktiviteten **när en HTTP-begäran tas emot** och kopierar sedan **http post-URL: en** som nu har genererats.  Detta är den URL som kan användas av Azure Data Factory för att göra det asynkrona anropet att utlösa Logic-appen.

Här är ett exempel Azure Data Factory webb aktivitet som utför den här åtgärden.

![Data Factory webb aktivitet](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Använda en självständig logisk app

Om du inte planerar att använda ett Orchestration-verktyg, till exempel Data Factory för att utlösa uppdatering av modellen, kan du ställa in Logic app så att uppdateringen utlöses baserat på ett schema.

Använd exemplet ovan och ta bort den första aktiviteten och ersätt den med en **schemalagd** aktivitet.

![Skärm bild som visar sidan "Logic Apps" där panelen "schema" är markerad.](./media/analysis-services-async-refresh-logic-app/12.png)

![Skärm bild som visar sidan "utlösare".](./media/analysis-services-async-refresh-logic-app/13.png)

I det här exemplet används **upprepning**.

När aktiviteten har lagts till konfigurerar du intervallet och frekvensen och lägger sedan till en ny parameter och väljer **vid dessa timmar**.

![Skärm bild som visar avsnittet "upprepning" med parametern "vid dessa timmar" vald.](./media/analysis-services-async-refresh-logic-app/16.png)

Välj önskade timmar.

![Schemalägg aktivitet](./media/analysis-services-async-refresh-logic-app/15.png)

Spara Logic-appen.

## <a name="next-steps"></a>Nästa steg

[Exempel](analysis-services-samples.md)  
[REST-API](/rest/api/analysisservices/servers)