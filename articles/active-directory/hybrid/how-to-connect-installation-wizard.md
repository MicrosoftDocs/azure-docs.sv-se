---
title: Kör installations guiden för Azure AD Connect | igen | Microsoft Docs
description: Förklarar hur installations guiden fungerar den andra gången du kör den.
keywords: I installations guiden för Azure AD Connect kan du konfigurera underhålls inställningar den andra gången du kör det
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306131"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect synkronisering: kör installations guiden en gång till
Första gången du kör installations guiden för Azure AD Connect får du stegvisa anvisningar om hur du konfigurerar installationen. Om du kör installations guiden igen får du alternativ för underhåll.

>[!IMPORTANT]
>Tänk på att du inte kan köra installations guiden medan en synkronisering pågår.  Kontrol lera att ingen synkronisering körs innan du startar guiden.

Du hittar installations guiden på Start-menyn med namnet **Azure AD Connect**.

![Start-menyn](./media/how-to-connect-installation-wizard/startmenu.png)

När du startar installations guiden visas en sida med följande alternativ:

![Sida med en lista över ytterligare aktiviteter](./media/how-to-connect-installation-wizard/additionaltasks.png)

Om du har installerat ADFS med Azure AD Connect har du ännu fler alternativ. De ytterligare alternativ som du har för ADFS dokumenteras i [ADFS-hantering](how-to-connect-fed-management.md#manage-ad-fs).

Välj en av uppgifterna och klicka på **Nästa** för att fortsätta.

> [!IMPORTANT]
> När du har installerat guiden pausas alla åtgärder i Synkroniseringsmotorn. Se till att stänga installations guiden så snart du har slutfört konfigurations ändringarna.
>
>

## <a name="view-current-configuration"></a>Visa aktuell konfiguration
Med det här alternativet får du en snabb överblick över dina för tillfället konfigurerade alternativ.

![Sida med en lista över alla alternativ och deras status](./media/how-to-connect-installation-wizard/viewconfig.png)

Klicka på **föregående** för att gå tillbaka. Om du väljer **Avsluta** stänger du installations guiden.

## <a name="customize-synchronization-options"></a>Anpassa synkroniseringsalternativ
Det här alternativet används för att göra ändringar i Sync-konfigurationen. Du ser en delmängd av alternativen i installations Sök vägen för anpassad konfiguration. Du ser det här alternativet även om du använde Express installation från början.

* [Lägg till fler kataloger](how-to-connect-install-custom.md#connect-your-directories). Information om hur du tar bort en katalog finns i [ta bort en anslutning](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Ändra domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Ta bort grupp filtrering.
* [Ändra valfria funktioner](how-to-connect-install-custom.md#optional-features).

De andra alternativen från den första installationen kan inte ändras och är inte tillgängliga. De här alternativen är:

* Ändra attributet som ska användas för userPrincipalName och sourceAnchor.
* Ändra metoden för att ansluta för objekt från olika skogar.
* Aktivera gruppbaserad filtrering.

## <a name="refresh-directory-schema"></a>Uppdatera katalog schema
Det här alternativet används om du har ändrat schemat i någon av dina lokala AD DS-skogar. Du kan till exempel ha installerat Exchange eller uppgraderat till ett Windows Server 2012-schema med enhets objekt. I så fall måste du instruera Azure AD Connect att läsa schemat igen från AD DS och uppdatera dess cacheminne. Den här åtgärden återskapar också reglerna för synkronisering. Om du lägger till Exchange-schemat läggs notifieringsregler för Exchange till i konfigurationen.

När du väljer det här alternativet visas alla kataloger i konfigurationen. Du kan behålla standardinställningen och uppdatera alla skogar eller avmarkera några av dem.

![Sida med en lista över alla kataloger i miljön](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurera mellanlagrings läge
Med det här alternativet kan du aktivera och inaktivera mellanlagrings läge på servern. Mer information om mellanlagrings läge och hur det kan användas finns i [åtgärder](how-to-connect-sync-staging-server.md).

Alternativet visar om mellanlagring för närvarande är aktiverat eller inaktiverat:  
![Skärm bild som visar mellanlagrings läge inaktiverat.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Om du vill ändra tillstånd väljer du det här alternativet och markerar eller avmarkerar kryss rutan.  
![Alternativ som också visar det aktuella läget för mellanlagrings läge](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Ändra användar inloggning
Med det här alternativet kan du ändra användar inloggnings metoden till och från hash-synkronisering av lösen ord, direktautentisering eller Federation. Du kan inte ändra till **Konfigurera inte**.

Mer information om det här alternativet finns i [användar inloggning](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om konfigurations modellen som används av Azure AD Connect Sync i att [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
