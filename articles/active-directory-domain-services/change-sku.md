---
title: 'Ändra SKU: n för en Azure AD Domain Services | Microsoft Docs'
description: Läs om SKU-nivån för en Azure AD Domain Services hanterad domän om dina affärs behov ändras
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620298"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Ändra SKU: n för en befintlig Azure Active Directory Domain Services hanterad domän

I Azure Active Directory Domain Services (Azure AD DS) baseras tillgängliga prestanda och funktioner på SKU-typen. Dessa funktions skillnader omfattar säkerhets kopierings frekvensen eller det maximala antalet enkelriktade utgående skogs förtroenden.

Du väljer en SKU när du skapar den hanterade domänen och du kan växla SKU: er upp eller ned när ditt företags behov ändras efter att den hanterade domänen har distribuerats. Ändringar i affärs kraven kan omfatta behovet av mer frekventa säkerhets kopieringar eller för att skapa ytterligare skogs förtroenden. Mer information om begränsningar och priser för olika SKU: er finns i [Azure AD DS-koncept][concepts-sku] och pris sidor för [Azure AD DS][pricing] .

Den här artikeln visar hur du ändrar SKU för en befintlig Azure AD DS-hanterad domän med hjälp av Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du slutföra självstudien för att [skapa och konfigurera en hanterad domän][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Begränsningar för SKU-ändringar

Du kan ändra SKU: er upp eller ned efter att den hanterade domänen har distribuerats. Men om du använder en resurs skog och har skapat enkelriktade utgående skogs förtroenden från Azure AD DS till en lokal AD DS-miljö finns det vissa begränsningar för ändrings åtgärden för SKU. *Premium* -och *Enterprise* -SKU: erna definierar en gräns för antalet förtroenden som du kan skapa. Du kan inte ändra till en SKU med en lägre Max gräns än vad du för närvarande har konfigurerat.

Exempel:

* Om du har skapat två skogs förtroenden på *Premium* -SKU: n kan du inte ändra till *standard* -SKU: n. *Standard* -SKU: n har inte stöd för skogs förtroenden.
* Eller, om du har skapat sju förtroenden på *Premium* -SKU: n, kan du inte ändra till *företags* -SKU: n. *Enterprise* -SKU: n stöder högst fem förtroenden.

Mer information om dessa begränsningar finns i [funktioner och begränsningar för Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Välj en ny SKU

Utför följande steg för att ändra SKU: n för en hanterad domän med hjälp av Azure Portal:

1. Sök efter och välj **Azure AD Domain Services** överst i Azure Portal. Välj den hanterade domänen i listan, till exempel *aaddscontoso.com*.
1. På menyn till vänster på sidan Azure AD DS väljer du **inställningar > SKU**.

    ![Välj meny alternativet SKU för din Azure AD DS-hanterade domän i Azure Portal](media/change-sku/overview-change-sku.png)

1. I den nedrullningsbara menyn väljer du den SKU som du vill använda för din hanterade domän. Om du har en resurs skog kan du inte välja *standard* -SKU som skogs förtroenden endast är tillgängliga på *företags* -SKU: n eller högre.

    Välj den SKU som du vill använda på den nedrullningsbara menyn och välj sedan **Spara**.

    ![Välj den SKU som krävs på den nedrullningsbara menyn i Azure Portal](media/change-sku/change-sku-selection.png)

Det kan ta en minut eller två att ändra SKU-typen.

## <a name="next-steps"></a>Nästa steg

Om du har en resurs skog och vill skapa ytterligare förtroenden när SKU-ändringen har ändrats, se [skapa en utgående skogs förtroende till en lokal domän i Azure AD DS][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
