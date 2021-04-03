---
title: 'Azure AD Connect: Välj Installations typ | Microsoft Docs'
description: Det här avsnittet beskriver hur du väljer vilken Installations typ som ska användas för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996618"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Välj vilken installationstyp du vill använda för Azure AD Connect
Azure AD Connect har två installations typer för en ny installation: Express och anpassad. Det här avsnittet hjälper dig att bestämma vilket alternativ som ska användas under installationen.

## <a name="express"></a>Express
Express är det vanligaste alternativet och används av cirka 90% av alla nya installationer. Den har utformats för att tillhandahålla en konfiguration som fungerar för de vanligaste kund scenarierna.

Det förutsätter:

- Du har en enda Active Directory skog lokalt.
- Du har ett företags administratörs konto som du kan använda för installationen.
- Du har färre än 100 000 objekt i din lokala Active Directory.

Du får:

- [Password-hash-synkronisering](how-to-connect-password-hash-synchronization.md) från lokal plats till Azure AD för enkel inloggning.
- En konfiguration som synkroniserar [användare, grupper, kontakter och Windows 10-datorer](concept-azure-ad-connect-sync-default-configuration.md).
- Synkronisering av alla kvalificerade objekt i alla domäner och alla organisationsenheter.
- [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) är aktiverat för att se till att du alltid använder den senaste tillgängliga versionen.

Alternativ där du fortfarande kan använda Express:

- Om du inte vill synkronisera alla organisationsenheter kan du fortfarande använda Express och på den sista sidan, avmarkera * * starta synkroniseringsprocessen... * * *. Kör sedan installations guiden igen och ändra organisationsenheterna i [konfigurations alternativen](how-to-connect-installation-wizard.md#customize-synchronization-options) och aktivera schemalagd synkronisering.
- Du vill aktivera en av funktionerna i Azure AD Premium, till exempel tillbakaskrivning av lösen ord. Börja med Express för att få den första installationen slutförd. Kör sedan installations guiden igen och ändra [konfigurations alternativen](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Anpassad
Den anpassade sökvägen tillåter många fler alternativ än Express. Den bör användas i samtliga fall där konfigurationen som beskrivs i föregående avsnitt för Express inte är representativ för din organisation.

Använd när:

- Du har inte åtkomst till ett företags administratörs konto i Active Directory.
- Du har mer än en skog eller så planerar du att synkronisera fler än en skog i framtiden.
- Det går inte att komma åt domäner i skogen från Connect-servern.
- Du planerar att använda Federation eller direktautentisering för användar inloggning.
- Du har fler än 100 000 objekt och behöver använda en fullständig SQL Server.
- Du planerar att använda gruppbaserad filtrering och inte bara domän-eller OU-baserad filtrering.

## <a name="upgrade-from-dirsync"></a>Uppgradera från DirSync
Om du för närvarande använder DirSync följer du stegen i [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) för att uppgradera den befintliga konfigurationen. Det finns två olika uppgraderings alternativ:

- Uppgradering på plats för att installera Connect på samma server.
- Parallell distribution för att installera Connect på en ny server medan den befintliga DirSync-servern fortfarande fungerar.

## <a name="upgrade-from-azure-ad-sync"></a>Uppgradera från Azure AD Sync
Om du för närvarande använder Azure AD Sync kan du följa [samma steg](how-to-upgrade-previous-version.md) som när du uppgraderar från en Connect-version till en nyare. Det finns två olika uppgraderings alternativ:

- Uppgradering på plats för att installera Connect på samma server.
- Flytta – migrering om du vill installera Connect på en ny server medan den befintliga Azure AD Sync servern fortfarande fungerar.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrera från FIM2010 eller MIM2016
Om du för närvarande använder Forefront Identity Manager 2010 eller Microsoft Identity Manager 2016 med Azure AD Connector är ditt enda alternativ en migrering. Följ stegen som beskrivs i flytta [– migrering](how-to-upgrade-previous-version.md#swing-migration). I stegen ersätter du eventuella omnämnanden av Azure AD Sync med FIM2010/MIM2016.

## <a name="next-steps"></a>Nästa steg
Beroende på vilket alternativ du har valt att använda använder du innehålls förteckningen till vänster för att hitta din artikel med detaljerade anvisningar.
