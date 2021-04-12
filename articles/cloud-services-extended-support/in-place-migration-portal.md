---
title: Migrera – portalen
description: Så här migrerar du till Cloud Services (utökad support) med hjälp av Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286956"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrera till Cloud Services (utökad support) med hjälp av Azure Portal

Den här artikeln visar hur du använder Azure Portal för att migrera från [Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md) till [Cloud Services (utökad support)](overview.md).

> [!IMPORTANT]
> Migrering från Cloud Services (klassisk) till Cloud Services (utökad support) med migreringsverktyget är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Innan du börjar

**Se till att du är administratör för prenumerationen.**

För att utföra den här migreringen måste du läggas till som en medadministratör för prenumerationen i [Azure Portal](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn **hubb** väljer du **prenumeration**. Om du inte ser det väljer du **alla tjänster**.
3. Hitta rätt prenumerations post och titta sedan i fältet **min roll** . För en-administratör ska värdet vara *konto administratör*.

Om du inte kan lägga till en medadministratör kan du kontakta en tjänst administratör eller delad [administratör](../role-based-access-control/classic-administrators.md) för prenumerationen för att få till gång till dig själv.

**Registrera dig för resurs leverantören för migrering**

1. Registrera dig för resurs leverantören `Microsoft.ClassicInfrastructureMigrate` och förhands gransknings funktionen i `Cloud Services` Microsoft. Compute-namnrymden med hjälp av [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Vänta fem minuter tills registreringen är klar kontrol lera statusen för godkännandet. 

## <a name="migrate-your-cloud-service-resources"></a>Migrera moln tjänst resurser

1. Gå till [bladet Cloud Services (klassisk)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Välj den moln tjänst som du vill migrera.
3. Välj bladet **migrera till arm** .

    > [!NOTE]
    > Om du migrerar en Cloud Services (klassisk) i ett virtuellt nätverk (klassisk) visas ett informations meddelande där du uppmanas att flytta det virtuella nätverket (klassiska) bladet.
    > Du kommer till det virtuella nätverkets blad (klassisk) för att slutföra migreringen av både det virtuella nätverket (klassisk) och de Cloud Services (klassiska) distributionerna i det.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Bild visar hur du flyttar ett virtuellt nätverk som är klassiskt i Azure Portal.":::
 

4. Verifiera migreringen. 

    Om verifieringen lyckas, så stöds alla distributioner och är redo att förberedas.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Bild visar bladet migrera till ARM i Azure Portal.":::

    Om verifieringen Miss lyckas visas en lista över scenarier som inte stöds och måste korrigeras innan migreringen kan fortsätta. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Bild som visar verifierings fel i Azure Portal.":::

5. Förbered för migreringen.

    Om förberedelsen lyckas är migreringen klar att genomföra.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Bild visar verifierings överföring i Azure Portal.":::

    Om förberedelsen Miss lyckas granskar du felet, åtgärdar eventuella problem och försöker förbereda igen. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="Bilden visar ett verifierings fel.":::

      Efter förberedelsen är alla Cloud Services i ett virtuellt nätverk tillgängliga för Läs åtgärder med både Cloud Services (klassisk) och Cloud Services (Extended support) Azure Portal bladet. Distributionen av moln tjänsten (utökad support) kan nu testas för att säkerställa att den fungerar korrekt innan migreringen slutförs. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Bilden visar test-API: er på Portal bladet.":::

6.  **(Valfritt)** Avbryt migreringen. 
    
    Om du väljer att avbryta migreringen använder du knappen **Avbryt** för att återställa föregående steg. Den Cloud Services (klassiska) distributionen är sedan olåst för alla CRUD-åtgärder.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Bild visar verifierings passning.":::

    Om det inte går att avbryta väljer du **försök att avbryta**. Ett nytt försök bör åtgärda problemet. Annars kontaktar du supporten. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Bild som visar fel meddelandet validerings fel.":::

7.  Genomför migrering.

    >[!IMPORTANT]
    > När du har bekräftat till migreringen finns det inget alternativ att återställa. 
    
    Skriv "Ja" för att bekräfta och bekräfta migreringen. Migreringen har slutförts. Distributionen av migrerade Cloud Services (utökad support) är olåst för alla åtgärder. 

## <a name="next-steps"></a>Nästa steg
Granska ändringar i avsnittet [efter migreringen](in-place-migration-overview.md#post-migration-changes) om du vill se ändringar i operativsystemfiler, automation och andra attribut för din nya Cloud Services (utökad support) distribution. 
