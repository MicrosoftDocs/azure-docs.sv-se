---
title: Felsöka ett attribut som inte synkroniseras i Azure AD Connect | Microsoft Docs
description: Det här avsnittet innehåller anvisningar för hur du felsöker problem med attributet synkronisering med hjälp av fel söknings aktiviteten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317495"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Felsöka ett attribut som inte synkroniseras i Azure AD Connect

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Innan du undersöker synkroniseringsproblem, ska vi förstå **Azure AD Connect** syncing-processen:

  ![Process för Azure AD Connect synkronisering](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** Kopplings utrymme, en tabell i en databas.
* **MV:** Metaversum, en tabell i databasen.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synkroniserings steg**

* Importera från AD: Active Directory objekt läggs till i AD CS.

* Importera från AAD: Azure Active Directory objekt samlas in i AAD CS.

* Synkronisering: regler för **inkommande synkronisering** och **regler för utgående synkronisering** körs i prioritetsordning från lägre till högre. Om du vill visa reglerna för synkronisering kan du gå till **Redigeraren för regler för synkronisering** från Skriv bords programmen. **Reglerna för inkommande synkronisering** samlar in data från CS till MV. **Reglerna för utgående synkronisering** flyttar data från MV till CS.

* Exportera till AD: När synkroniseringen har körts, exporteras objekten från AD CS till **Active Directory**.

* Exportera till AAD: När synkroniseringen har körts, exporteras objekt från AAD CS till **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Steg för steg-undersökning**

* Vi kommer att starta vår sökning från **metaversum** och titta på mappningen av attribut från källa till mål.

* Starta **Synchronization Service Manager** från Skriv bords programmen, som du ser nedan:

  ![Starta Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Välj **metaversum-sökningen** på **Synchronization Service Manager**, Välj **omfång efter objekt typ**, välj objektet med ett attribut och klicka på knappen **Sök** .

  ![Metaversum-sökning](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dubbelklicka på objektet som finns i **metaversum** -sökningen för att visa alla dess attribut. Du kan klicka på fliken **anslutningar** om du vill titta på motsvarande objekt i alla **kopplings utrymmen**.

  ![Metaversum objekt kopplingar](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dubbelklicka på **Active Directory koppling** för att visa attribut för **kopplings utrymme** . Klicka på knappen **Förhandsgranska** i följande dialog ruta och klicka på knappen **generera förhands granskning** .

  ![Skärm bild som visar skärmen för objekt egenskaper för kopplings utrymme med knappen förhands granskning markerad.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Klicka på **flödet importera attribut**, detta visar indata-attribut från **Active Directory kopplings utrymme** till **metaversum**. I kolumnen **Synkronisera regel** visas vilken **Synkroniseringsregel** som bidragit till attributet. I kolumnen **data källa** visas attributen från **anslutnings utrymmet**. I kolumnen **metaversum** visas attributen i **metaversum**. Du kan söka efter attributet som inte synkroniseras här. Om du inte hittar attributet här mappas det inte och du måste skapa en ny anpassad **Synkroniseringsregel** för att mappa attributet.

  ![Attribut för kopplings utrymme](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klicka på **flödet exportera attribut** i det vänstra fönstret för att visa **metaversum** tillbaka till **Active Directory anslutnings utrymme** med **regler för utgående synkronisering**.

  ![Skärm bild som visar metaversum från tillbaka till Active Directory anslutnings utrymme med regler för utgående synkronisering.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* På samma sätt kan du Visa **Azure Active Directory kopplings utrymmes** objekt och kan generera **förhands granskningen** för att visa ett attributarkiv från **metaversum** till **anslutnings utrymmet** och vice versa, på det här sättet kan du undersöka varför ett attribut inte synkroniseras.

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Azure AD Connect synkronisering: tekniska begrepp](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect synkronisering: förstå arkitekturen](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect synkronisering: förstå deklarativ etablering](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect synkronisering: förstå uttryck för deklarativ etablering](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect synkronisering: förstå användare, grupper och kontakter](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect synkronisering: skugg-attribut](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrid identitet?](whatis-hybrid-identity.md).