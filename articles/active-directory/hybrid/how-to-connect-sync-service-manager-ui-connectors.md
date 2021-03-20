---
title: Anslutningar i Azure AD Synchronization Service Manager UI | Microsoft Docs
description: Förstå fliken anslutningar i Synchronization Service Manager för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91319885"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Använda kopplingar med Azure AD Connect Sync-Service Manager

![Skärm bild som visar synkroniseringen Service Manager.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Fliken anslutningar används för att hantera alla system som Synkroniseringsmotorn är ansluten till.

## <a name="connector-actions"></a>Kopplings åtgärder
| Action | Kommentar |
| --- | --- |
| Skapa |Använd inte. Använd installations guiden för att ansluta till ytterligare AD-skogar. |
| Egenskaper |Används för domän-och OU-filtrering. |
| [Ta bort](#delete) |Används för att antingen ta bort data i anslutnings utrymmet eller ta bort anslutningen till en skog. |
| [Konfigurera körnings profiler](#configure-run-profiles) |Förutom för domän filtrering, inget att konfigurera här. Du kan använda den här åtgärden för att se redan konfigurerade körnings profiler. |
| Kör |Används för att starta en ett-off-körning av en profil. |
| Stoppa |Stoppar en anslutning som för närvarande kör en profil. |
| Exportera koppling |Använd inte. |
| Importera koppling |Använd inte. |
| Uppdatera anslutning |Använd inte. |
| Uppdatera schema |Uppdaterar det cachelagrade schemat. Det rekommenderas att du använder alternativet i installations guiden i stället, eftersom även uppdaterar regler för synkronisering. |
| [Sök efter anslutnings utrymme](#search-connector-space) |Används för att hitta objekt och följa ett objekt och dess data genom systemet. |

### <a name="delete"></a>Ta bort
Åtgärden ta bort används för två olika saker.  
![Skärm bild som visar fönstret "ta bort koppling" med alternativet "ta bort endast kopplings utrymme" markerat.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Alternativet **ta bort anslutnings utrymme tar bara** bort alla data, men behåll konfigurationen.

Alternativet **ta bort koppling och kopplings utrymme** tar bort data och konfigurationen. Det här alternativet används när du inte vill ansluta till en skog längre.

Båda alternativen synkroniserar alla objekt och uppdaterar metaversum-objekten. Den här åtgärden är en tids krävande åtgärd.

### <a name="configure-run-profiles"></a>Konfigurera körnings profiler
Med det här alternativet kan du se de körnings profiler som kon figurer ATS för en anslutning.

![Skärm bild som visar fönstret "Konfigurera körnings profiler" med "delta import" markerat.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Sök efter anslutnings utrymme
Åtgärden Sök kopplings utrymme är användbar för att hitta objekt och felsöka data problem.

![Skärm bild som visar fönstret "Sök kopplings utrymme".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Börja med att välja ett **omfång**. Du kan söka baserat på data (RDN, DN, ankare, under träd) eller tillstånd för objektet (alla andra alternativ).  
![Skärm bild som visar den nedrullningsbara menyn "omfång".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Om du till exempel gör en Sub-Tree Sök, får du alla objekt i en ORGANISATIONSENHET.  
![Skärm bild som visar ett exempel på en "under träd"-sökning.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Från det här rutnätet kan du välja ett objekt, välja **Egenskaper** och [följa det](tshoot-connect-object-not-syncing.md) från källans kopplings utrymme, via metaversum och till mål kopplings utrymmet.

### <a name="changing-the-ad-ds-account-password"></a>Ändra AD DS-kontolösenordet
Om du ändrar lösen ordet för kontot kommer synkroniseringstjänsten inte längre att kunna importera/exportera ändringar till lokala AD.   Du kan se följande:

- Import/export-steget för AD-anslutningen Miss lyckas med fel meddelandet "inga-start-Credential".
- Under Windows Loggboken innehåller program händelse loggen ett fel med händelse-ID 6000 och meddelandet "hanterings agentens" contoso.com "kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga."

Lös problemet genom att uppdatera AD DS-användarkontot med följande:


1. Starta Synchronization Service Manager (START → synkroniseringstjänst).
</br>![Synkronisera Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Gå till fliken **anslutningar** .
3. Välj den AD-anslutning som är konfigurerad för att använda AD DS-kontot.
4. Under åtgärder väljer du **Egenskaper**.
5. I popup-dialogrutan väljer du Anslut till Active Directory skog:
6. Skogs namnet anger motsvarande lokal AD.
7. Användar namnet anger det AD DS-konto som används för synkronisering.
8. Ange det nya lösen ordet för AD DS-kontot i text rutan lösen ord ![ Azure AD Connect verktyget Sync Encryption Key](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klicka på OK för att spara det nya lösen ordet och starta om synkroniseringstjänsten för att ta bort det gamla lösen ordet från cacheminnet.



## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
