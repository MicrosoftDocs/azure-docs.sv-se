---
title: Konto hantering – Azure VMware-lösning av CloudSimple-portalen
description: Beskriver hur du hanterar konton på Azure VMware-lösningen från CloudSimple-portalen
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4c26d5accce77ce6fd8c9b6c2b519b93f95013ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895180"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Hantera konton på Azure VMware-lösningen från CloudSimple-portalen

När du skapar din CloudSimple-tjänst skapas ett konto på CloudSimple. Kontot är associerat med din Azure-prenumeration där tjänsten finns. Alla användare med rollen ägare och deltagare i prenumerationen har åtkomst till CloudSimple-portalen. Det ID för Azure-prenumeration och klient-ID som är associerat med CloudSimple-tjänsten finns på konto sidan.

Om du vill hantera konton i CloudSimple-portalen går [du till portalen](access-cloudsimple-portal.md) och väljer **konto** på menyn på sidan.

Välj **Sammanfattning** om du vill visa information om ditt företags CloudSimple-konfiguration. Den aktuella kapaciteten för moln konfigurationen visas, inklusive antal privata moln, totalt lagrings utrymme, kluster konfiguration för vSphere, antal noder och antalet beräknings kärnor. En länk ingår för att köpa ytterligare noder om den aktuella konfigurationen inte uppfyller alla dina behov.

## <a name="email-alerts"></a>E-postaviseringar

Du kan lägga till e-postadresser till alla personer som du vill meddela om ändringar i konfigurationen för det privata molnet.

1. Klicka på **Lägg till nytt** i avsnittet **ytterligare e-postaviseringar** .
2. Ange e-postadressen.
3. Tryck på RETUR.  

Klicka på **X** om du vill ta bort en post.

## <a name="cloudsimple-operator-access"></a>Åtkomst till CloudSimple-operatör

Med åtkomst inställningen operatör kan CloudSimple hjälpa dig med fel sökning genom att tillåta en support tekniker att logga in på din CloudSimple-Portal.  Inställningen är aktive rad som standard. Alla åtgärder som utförs av support teknikern när de är inloggade på ditt kund konto registreras och är tillgängliga för din granskning på sidan **aktivitets**  >  **granskning** .

Klicka på **CloudSimple operatörs åtkomst aktive rad** växla för att aktivera eller inaktivera åtkomst.
