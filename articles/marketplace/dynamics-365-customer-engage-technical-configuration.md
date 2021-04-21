---
title: Konfigurera Dynamics 365 for Customer Engagement & PowerApps erbjuder teknisk konfiguration på Microsoft AppSource (Azure Marketplace)
description: Konfigurera Dynamics 365 for Customer Engagement & PowerApps erbjuder teknisk konfiguration på Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820472"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Konfigurera Teknisk konfiguration för Dynamics 365 for Customer Engagement & Power Apps ett erbjudande

Den här sidan definierar den tekniska information som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi etablera ditt erbjudande för slutanvändaren om de väljer att skaffa det.

## <a name="offer-information"></a>Erbjudandeinformation

**Baslicensmodellen** avgör hur kunder tilldelas ditt program i CRM-administrationscentret. Välj **Resurs** för instansbaserad licensiering eller Användare **om** licenserna tilldelas en per klientorganisation.

Kryssrutan **Kräver utgående S2S-** och CRM Secure Store-åtkomst aktiverar konfiguration av utgående åtkomst för CRM Secure Store eller S2S (Server-till-Server). Den här funktionen kräver särskild uppmärksamhet från Dynamics 365-teamet under certifieringsfasen. Microsoft kontaktar dig för att slutföra ytterligare steg för att stödja den här funktionen.

Lämna **URL för programkonfiguration** tomt. det är för framtida användning.

## <a name="crm-package"></a>CRM-paket

För **URL för paketplatsen anger du** URL:en för ett Azure Blob Storage som innehåller den uppladdade .zip-filen för CRM-paketet. Inkludera en skrivskyddade SAS-nyckel i URL:en så att Microsoft kan hämta ditt paket för verifiering.

> [!IMPORTANT]
> För att undvika ett publiceringsblock kontrollerar du att förfallodatumet i URL:en för bloblagringen inte har upphört att gälla. Du kan ändra datumet genom att öppna principen. Vi rekommenderar **att förfallotiden** är minst en månad i framtiden.

Välj rutan **Det finns fler än ett CRM-paket i paketfilen om** det är tillämpligt. I så fall måste du inkludera alla paket i zip-filen.

Detaljerad information om hur du skapar ditt paket och uppdaterar dess struktur finns i [Steg 3: Skapa ett AppSource-paket för din app.](/powerapps/developer/common-data-service/create-package-app-appsource)

## <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

Välj **+ Lägg till region** för att ange de geografiska regioner där ditt CRM-paket ska vara tillgängligt för kunder. Distribution till följande suveräna regioner kräver särskild behörighet och verifiering under certifieringsprocessen: [Tyskland,](../germany/index.yml) [US Government Cloud](../azure-government/documentation-government-welcome.md)och TIPS.

Som standard används den **PROGRAMkonfigurations-URL** som du angav ovan för varje region. Om du vill kan du ange en separat programkonfigurations-URL för en eller flera specifika regioner.

Välj **Spara utkast** innan du fortsätter till nästa flik i den vänstra navigeringsmenyn, **Co-sell with Microsoft (Sälj sälj med Microsoft).** Information om hur du ställer in samförsäljning med Microsoft (valfritt) finns [i Co-sell partner engagement (Samförsäljningspartnergagemang).](marketplace-co-sell.md) Om du inte ställer in säljförsäljning eller om du är klar fortsätter du med **Nästa steg** nedan.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera kompletterande innehåll](dynamics-365-customer-engage-supplemental-content.md)
