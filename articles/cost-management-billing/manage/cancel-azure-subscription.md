---
title: Avbryta din Azure-prenumeration
description: Beskriver hur du avbryter din Azure-prenumeration, till exempel den kostnadsfria utvärderingsprenumerationen
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 662e1bf721c93ee1d59946d2fd603551f3f88ad3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503802"
---
# <a name="cancel-your-azure-subscription"></a>Avbryta din Azure-prenumeration

Du kan avsluta din Azure-prenumeration i Microsoft Azure-portalen om du inte längre behöver den.

Även om det inte krävs *rekommenderar* Microsoft att du vidtar följande åtgärder innan du avbryter din prenumeration:

* Säkerhetskopiera dina data. Om du till exempel lagrar data i Azure Storage eller SQL kan du ladda ned en kopia. Om du har en virtuell dator sparar du en avbildning av den lokalt.
* Stäng av dina tjänster. Gå till sidan [resurssidan i hanteringsportalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) och **Stoppa** eventuella virtuella datorer, program eller andra tjänster som körs.
* Överväg att migrera dina data. Se [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Ta bort alla resurser och alla resursgrupper.
* Om du har anpassade roller som refererar till den här prenumerationen i `AssignableScopes` bör du uppdatera dessa anpassade roller för att ta bort prenumerationen. Om du försöker uppdatera en anpassad roll efter att du har avbrutit en prenumeration visas kanske ett felmeddelande. Mer information finns i [Felsöka problem med anpassade roller](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) och [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md).

Om du avslutar ett betalt supportavtal för Azure debiteras du för resten av prenumerationsperioden. Mer information finns i [Azure-supportplaner](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Vem kan avsluta en prenumeration?

I tabellen nedan beskrivs den behörighet som krävs för att avbryta en prenumeration.

|Prenumerationstyp     |Vem som kan avbryta  |
|---------|---------|
|Prenumerationer som skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Kontoadministratör och ägare för prenumerationen  |
|[Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) och [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Kontoinnehavare och ägare för prenumerationen       |
|[Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/) och [Azure-plan för DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Ägare av prenumerationen      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Avsluta prenumeration i Azure-portalen

1. Välj din prenumeration på [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill avsluta.
1. Välj **Översikt** och sedan **Avbryt prenumeration**.
    ![Skärmbild som visar knappen Avbryt](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Följ anvisningarna och slutför annulleringen.

> [!NOTE]
> Partner kan pausa eller avbryta en prenumeration om det begärs av en kund eller i fall av utebliven betalning eller bedrägerier. Mer information finns i [Pausa eller avbryta en prenumeration](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Avsluta ett supportavtal

Om du köpte ditt supportavtal på Azures webbplats eller på Azure-portalen, eller om du har ett supportavtal genom ett Microsoft-kundavtal, kan du avsluta det. Om du köpte ditt supportavtal från en Microsoft-representant eller Microsoft-partner kontaktar du representanten eller partnern för att få hjälp. 

1. Gå till **Cost Management + Billing** på Azure-portalen.
1. Välj **Återkommande avgifter** under **Fakturering**.
1. Välj ellipsen ( **...** ) till höger om radartikeln för supportavtalet och välj **Stäng av automatisk förnyelse**.

## <a name="what-happens-after-subscription-cancellation"></a>Vad händer när prenumerationen har avbrutits?

När du har avbrutit stoppas faktureringen omedelbart. Det kan dock ta upp till 10 minuter innan annulleringen visas i portalen. Om du avbryter mitt i en faktureringsperiod skickar vi den sista fakturan på ditt vanliga faktureringsdatum när perioden är slut.

När du har avbrutit inaktiveras dina tjänster. Det innebär att dina virtuella datorer avallokeras, temporära IP-adresser frigörs och lagringen skrivskyddas.

När prenumerationen har avslutats väntar Microsoft 30–90 dagar innan data tas bort permanent, om du skulle behöva komma åt dem eller om du ångrar dig. Vi debiterar dig inte för att behålla dessa data. Mer information finns i [Microsoft Trust Center – Så hanterar vi dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Ta bort kostnadsfri utvärderingsprenumeration

Om du har en kostnadsfri utvärderingsprenumeration behöver du inte vänta 30 dagar på att prenumerationen ska tas bort automatiskt. Du kan ta bort prenumerationen *tre dagar* efter att du har avslutat den. Alternativet **Ta bort prenumeration** är inte tillgängligt förrän tre dagar efter att du har avslutat din prenumeration.

1. Vänta tre dagar efter det datum då du avslutade prenumerationen.
1. Välj din prenumeration på [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) på Azure-portalen.
1. Välj den prenumeration som du vill ta bort.
1. Välj **Översikt** och välj sedan **Ta bort prenumeration**.

## <a name="reactivate-a-subscription"></a>Återaktivera en prenumeration

Om du oavsiktligt avbryter prenumerationen med Betala per användning-priser kan du [återaktivera den i Kontocenter](subscription-disabled.md).

Om din prenumeration inte är en prenumeration med Betala per användning-priser kontaktar du supporten inom 90 dagar efter annulleringen för att återaktivera prenumerationen.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Varför visas inte alternativet Avsluta prenumeration i Microsoft Azure-portalen? 

Du kanske inte har behörigheterna som krävs för att avsluta en prenumeration. En beskrivning av vilka som kan avsluta olika typer av prenumerationer finns i [Vem kan avsluta en prenumeration?](#who-can-cancel-a-subscription).

## <a name="how-do-i-delete-my-azure-account"></a>Hur gör jag för att ta bort mitt Azure-konto?

*Jag behöver ta bort mitt konto, inklusive all personlig information. Jag har redan avslutat mina aktiva prenumerationer (kostnadsfria utvärderingar). Jag har inga aktiva prenumerationer och vill ta bort mitt konto fullständigt*.

* Om du har ett Azure Active Directory-konto via din organisation kan Azure AD-administratören ta bort ditt konto. Efter detta inaktiveras dina tjänster. Det innebär att dina virtuella datorer avallokeras, temporära IP-adresser frigörs och lagringen skrivskyddas. Sammanfattningsvis: när du har avslutat prenumerationen stoppas faktureringen omedelbart.

* Om du inte har ett Azure AD-konto via din organisation kan du avsluta och sedan ta bort dina Azure-prenumerationer, och sedan ta bort ditt kreditkort från kontot. Även om dessa åtgärder inte tar bort kontot gör de så att kontot inte kan användas längre. Du kan gå ett steg längre och även ta bort det associerade Microsoft-kontot om det inte heller används längre.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Hur gör jag för att avsluta ett Visual Studio Professional-konto?

Se artikeln [Förnya och avsluta](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Om du har några Visual Studio Azure-prenumerationer måste de också avslutas och tas bort.

## <a name="next-steps"></a>Nästa steg

- Om så behövs kan du återaktivera en betala per användning-prenumeration i [kontocentret](subscription-disabled.md).