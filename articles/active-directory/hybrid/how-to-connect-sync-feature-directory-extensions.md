---
title: 'Azure AD Connect synkronisering: Katalog tillägg | Microsoft Docs'
description: I det här avsnittet beskrivs funktionen katalog tillägg i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976885"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synkronisering: Katalog tillägg
Du kan använda katalog tillägg för att utöka schemat i Azure Active Directory (Azure AD) med dina egna attribut från lokala Active Directory. Med den här funktionen kan du bygga LOB-appar genom att förbruka attribut som du fortsätter att hantera lokalt. Dessa attribut kan användas via [tillägg](/graph/extensibility-overview
). Du kan se tillgängliga attribut med hjälp av [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Du kan också använda den här funktionen för att skapa dynamiska grupper i Azure AD.

Ingen Microsoft 365 arbets belastning förbrukar dessa attribut.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Anpassa vilka attribut som ska synkroniseras med Azure AD

Du konfigurerar vilka ytterligare attribut som du vill synkronisera i sökvägen för anpassade inställningar i installations guiden.

> [!NOTE]
> I Azure AD Connect versioner tidigare än 1.2.65.0 är sökrutan för **tillgängliga attribut** Skift läges känslig.

![Guiden schema tillägg](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

I installationen visas följande attribut, som är giltiga kandidater:

* Användar-och grupp objekt typer
* Attribut med enkel värde: sträng, boolesk, heltal, binär
* Attribut med flera värden: sträng, binär


>[!NOTE]
> När Azure AD Connect synkroniserat multi-Value Active Directory-attribut till Azure AD som ett tillägg med flera värden, är det möjligt att inkludera attribut för SAML-anspråket. Men det går inte att använda dessa data via API-anrop.

Listan med attribut läses från det schema-cacheminne som skapas under installationen av Azure AD Connect. Om du har utökat Active Directory schema med ytterligare attribut måste du [uppdatera schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut visas.

Ett objekt i Azure AD kan ha upp till 100 attribut för katalog tillägg. Den maximala längden är 250 tecken. Om ett attributvärde är längre, trunkerar Synkroniseringsmotorn det.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Konfigurations ändringar i Azure AD som skapats av guiden

Under installationen av Azure AD Connect registreras ett program där dessa attribut är tillgängliga. Du kan se det här programmet i Azure Portal. Namnet är alltid **appen för schema tillägg för klient organisationer**.

![App för schema tillägg](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Se till att du väljer **alla program** för att se den här appen.

Attributen föregås av **tillägget \_ {ApplicationId} \_**. ApplicationId har samma värde för alla attribut i din Azure AD-klient. Du behöver det här värdet för alla andra scenarier i det här avsnittet.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Visa attribut med hjälp av Microsoft Graph API

De här attributen är nu tillgängliga via Microsoft Graph API, med [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> I Microsoft Graph API måste du be om vilka attribut som ska returneras. Välj uttryckligen attribut så här: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Mer information finns i [Microsoft Graph: Använd frågeparametrar](/graph/query-parameters#select-parameter).

>[!NOTE]
> Det går inte att synkronisera attributvärden från AADConnect till utöknings bara attribut som inte har skapats av AADConnect. Detta kan orsaka prestanda problem och oväntade resultat. Endast utöknings bara attribut som skapas på det sätt som anges ovan stöds för synkronisering.

## <a name="use-the-attributes-in-dynamic-groups"></a>Använda attributen i dynamiska grupper

Ett av de mer användbara scenarierna är att använda attributen i dynamiska säkerhets-eller Microsoft 365s grupper.

1. Skapa en ny grupp i Azure AD. Ge det ett lämpligt namn och se till att **medlemskaps typen** är **dynamisk användare**.

   ![Skärm bild med en ny grupp](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Välj om du vill **lägga till dynamisk fråga**. Om du tittar på egenskaperna visas inte dessa utökade attribut. Du måste först lägga till dem. Klicka på **Hämta anpassade tilläggs egenskaper**, ange program-ID och klicka på **Uppdatera egenskaper**.

   ![Skärm bild där katalog tillägg har lagts till](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Öppna List rutan egenskap och Observera att de attribut som du har lagt till nu visas.

   ![Skärm bild med nya attribut som visas i användar gränssnittet](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Fyll i uttrycket så att det passar dina behov. I vårt exempel är regeln inställd på **(User.extension_9d98ed114c4840d298fad781915f27e4_division-EQ "försäljning och marknadsföring")**.

4. När gruppen har skapats kan du ge Azure AD lite tid för att fylla medlemmarna och sedan granska medlemmarna.

   ![Skärm bild med medlemmar i den dynamiska gruppen](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
