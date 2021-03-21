---
title: Fel sökning för Datadog – Azure-partner lösningar
description: Den här artikeln innehåller information om fel sökning av Datadog på Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563716"
---
# <a name="troubleshooting-datadog-on-azure"></a>Felsöka Datadog på Azure

Det här dokumentet innehåller information om hur du felsöker dina lösningar som använder Datadog.

## <a name="purchase-errors"></a>Köp fel

* Köpet Miss lyckas eftersom ett giltigt kredit kort inte är anslutet till Azure-prenumerationen eller så är ingen betalnings metod kopplad till prenumerationen.

  Använd en annan Azure-prenumeration. Eller Lägg till eller uppdatera kredit kortet eller betalnings metoden för prenumerationen. Mer information finns i [Uppdatera kredit-och betalnings metoden](../../cost-management-billing/manage/change-credit-card.md).

* EA-prenumerationen tillåter inte Marketplace-inköp.

  Använd en annan prenumeration. Eller kontrol lera om din EA-prenumeration är aktive rad för Marketplace-köp. Mer information finns i [Aktivera inköp på Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Om dessa alternativ inte löser problemet kan du kontakta [Datadog-supporten](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Det gick inte att skapa Datadog-resurs

Om du vill konfigurera Azure Datadog-integrering måste du ha **ägar** åtkomst till Azure-prenumerationen. Se till att du har rätt åtkomst innan du startar installationen.

## <a name="single-sign-on-errors"></a>Fel vid enkel inloggning

**Det går inte att spara inställningar för enkel inloggning** – det här felet inträffar när det finns en annan företags-app som använder SAML-identifieraren för Datadog. Om du vill ta reda på vilken app som använder den väljer du **Redigera** i avsnittet grundläggande SAML-konfiguration.

Lös problemet genom att antingen inaktivera den andra appen eller använda den andra appen som företags program för att konfigurera SAML SSO med Datadog. Om du väljer att använda den andra appen måste du se till att appen har de [inställningar som krävs](create.md#configure-single-sign-on).

**Appen visas inte på inställnings sidan för enkel inloggning** . Sök efter program-ID: t. Om inget resultat visas kontrollerar du appens SAML-inställningar. Rutnätet visar endast appar med rätt SAML-inställningar. 

URL: en för ID måste vara `https://us3.datadoghq.com/account/saml/metadata.xml` .

Svars-URL: en måste vara `https://us3.datadoghq.com/account/saml/assertion` .

Följande bild visar rätt värden.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Kontrol lera SAML-inställningarna för Datadog-programmet i AAD." border="true":::

**Gäst användare som bjudits in till klienten kan inte komma åt enkel inloggning** – vissa användare har två e-postadresser i Azure Portal. Vanligt vis är ett e-postmeddelande User Principal Name (UPN) och det andra e-postmeddelandet är ett alternativt e-postmeddelande.

När du bjuder in gäst användare använder du hem-klientens UPN. Med hjälp av UPN behåller du e-postadressen synkroniserad under processen för enkel inloggning. Du kan hitta UPN genom att leta efter e-postadressen i det övre högra hörnet av användarens Azure Portal.
  
## <a name="logs-not-being-emitted"></a>Loggar genereras inte

Endast resurser som anges i Azure Monitor resurs logg kategorier genererar loggar till Datadog. Du kan kontrol lera om resursen avger loggar till Datadog genom att navigera till Azure-diagnostikinställningar för den aktuella resursen. Kontrol lera att det finns en Datadog-diagnostisk inställning.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Datadog diagnostikinställningar på Azure-resursen" border="true":::

## <a name="metrics-not-being-emitted"></a>Mått som inte genereras

Datadog-resursen tilldelas en **övervaknings läsar** roll i lämplig Azure-prenumeration. Med den här rollen kan Datadog-resursen samla in mått och skicka dessa mått till Datadog.

Kontrol lera att resursen har rätt roll tilldelning genom att öppna Azure Portal och välja prenumerationen. I den vänstra rutan väljer du **Access Control (IAM)**. Sök efter Datadog resurs namn. Bekräfta att Datadog-resursen har tilldelningen av **övervaknings läsaren** som visas nedan.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Datadog roll tilldelning i Azure-prenumerationen" border="true":::

## <a name="datadog-agent-installation-fails"></a>Installationen av Datadog-agenten Miss lyckas

Med Azure Datadog-integreringen kan du installera Datadog-agenten på en virtuell dator eller App Service. För att konfigurera Datadog-agenten används den API-nyckel som valts som **standard nyckel** på skärmen API-nycklar. Om en standard nyckel inte har valts kommer Datadog Agent-installationen att Miss sen.

Om Datadog-agenten har kon figurer ATS med en felaktig nyckel går du till skärmen API-nycklar och ändrar **standard nyckeln**. Du måste avinstallera Datadog-agenten och installera om den för att konfigurera den virtuella datorn med de nya API-nycklarna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att hantera din instans](manage.md) av Datadog.
