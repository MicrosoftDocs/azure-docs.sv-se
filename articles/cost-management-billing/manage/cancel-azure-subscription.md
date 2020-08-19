---
title: Avbryta din Azure-prenumeration
description: Beskriver hur du avbryter din Azure-prenumeration, till exempel den kostnadsfria utvärderingsprenumerationen
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: banders
ms.openlocfilehash: 592a5d9573cb4e475c5d1ca6b4f06002f949e197
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136260"
---
# <a name="cancel-your-azure-subscription"></a>Avbryta din Azure-prenumeration

Du kan avbryta din Azure-prenumeration i Azure-portalen om du inte längre behöver prenumerationen.

Även om det inte krävs *rekommenderar* Microsoft att du vidtar följande åtgärder innan du avbryter din prenumeration:

* Säkerhetskopiera dina data. Om du till exempel lagrar data i Azure Storage eller SQL kan du ladda ned en kopia. Om du har en virtuell dator sparar du en avbildning av den lokalt.
* Stäng av dina tjänster. Gå till sidan [resurssidan i hanteringsportalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) och **Stoppa** eventuella virtuella datorer, program eller andra tjänster som körs.
* Överväg att migrera dina data. Se [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Ta bort alla resurser och alla resursgrupper.
* Om du har anpassade roller som refererar till den här prenumerationen i `AssignableScopes` bör du uppdatera dessa anpassade roller för att ta bort prenumerationen. Om du försöker uppdatera en anpassad roll efter att du har avbrutit en prenumeration visas kanske ett felmeddelande. Mer information finns i [Felsöka problem med anpassade roller](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) och [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md).

Om du avslutar ett betalt supportavtal för Azure debiteras du för resten av prenumerationsperioden. Mer information finns i [Azure-supportplaner](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Avsluta prenumeration i Azure-portalen

1. Välj din prenumeration på [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill avsluta.
1. Välj **Översikt** och sedan **Avbryt prenumeration**.
    ![Skärmbild som visar knappen Avbryt](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Följ anvisningarna och slutför annulleringen.

## <a name="who-can-cancel-a-subscription"></a>Vem kan avsluta en prenumeration?

I tabellen nedan beskrivs den behörighet som krävs för att avbryta en prenumeration.

|Prenumerationstyp     |Vem som kan avbryta  |
|---------|---------|
|Prenumerationer som skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Kontoadministratör och ägare för prenumerationen  |
|[Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) och [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Kontoinnehavare och ägare för prenumerationen       |
|[Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/) och [Azure-plan för DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Ägare av prenumerationen      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag har sagt upp min prenumeration?

När du har avbrutit stoppas faktureringen omedelbart. Det kan dock ta upp till 10 minuter innan annulleringen visas i portalen. Om du avbryter mitt i en faktureringsperiod skickar vi den sista fakturan på ditt vanliga faktureringsdatum när perioden är slut.

När du har avbrutit inaktiveras dina tjänster. Det innebär att dina virtuella datorer avallokeras, temporära IP-adresser frigörs och lagringen skrivskyddas.

När prenumerationen har avslutats väntar Microsoft 30–90 dagar innan data tas bort permanent, om du skulle behöva komma åt dem eller om du ångrar dig. Vi debiterar dig inte för bevarade data. Mer information finns i [Microsoft Trust Center – Så hanterar vi dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Ta bort kostnadsfri utvärderingsprenumeration

Om du har en kostnadsfri utvärderingsprenumeration behöver du inte vänta 30 dagar på att prenumerationen ska tas bort automatiskt. Du kan ta bort prenumerationen *tre dagar* efter att du har avslutat den. Alternativet **Ta bort prenumeration** är inte tillgängligt förrän tre dagar efter att du har avslutat din prenumeration.

1. Vänta tre dagar efter det datum då du avslutade prenumerationen.
1. Välj din prenumeration på [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) på Azure-portalen.
1. Välj den prenumeration som du vill ta bort.
1. Välj **Översikt** och välj sedan **Ta bort prenumeration**.

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du oavsiktligt avbryter prenumerationen med Betala per användning-priser kan du [återaktivera den i Kontocenter](subscription-disabled.md).

Om din prenumeration inte är en prenumeration med Betala per användning-priser kontaktar du supporten inom 90 dagar efter annulleringen för att återaktivera prenumerationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Om så behövs kan du återaktivera en betala per användning-prenumeration i [kontocentret](subscription-disabled.md).