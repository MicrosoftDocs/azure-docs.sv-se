---
title: Så här säkerställer du verifiering av Azure Database for PostgreSQL data kryptering
description: Lär dig hur du verifierar krypteringen av kryptering av Azure Database for PostgreSQL data med hjälp av den hanterade nyckeln för kunder.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 7ec27cc4f28151214ca97ffb5113607d6b60ee36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240587"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Verifierar data kryptering för Azure Database for PostgreSQL

Den här artikeln hjälper dig att kontrol lera att data kryptering med kund hanterad nyckel för Azure Database for PostgreSQL fungerar som förväntat.

## <a name="check-the-encryption-status"></a>Kontrol lera krypterings status

### <a name="from-portal"></a>Från portalen

1. Följ dessa steg om du vill kontrol lera att kundens nyckel används för kryptering:

    * I Azure Portal navigerar du till **Azure Key Vault**  ->  **nycklar**
    * Välj den nyckel som används för server kryptering.
    * Ange status för nyckeln som **är aktive rad** till **Nej**.
  
       Efter en stund (**~ 15 min**) ska Azure Database for postgresql server **status** vara **otillgänglig**. Alla I/O-åtgärder som utförts mot servern Miss Miss kan verifiera att servern verkligen är krypterad med kund nyckeln och att nyckeln inte är giltig för tillfället.
    
        För att göra servern **tillgänglig** för kan du verifiera om nyckeln. 
    
    * Ange statusen för nyckeln i Key Vault till **Ja**.
    * På Server **data krypteringen** väljer du **revalidate Key**.
    * När den omverifieringen av nyckeln har slutförts ändras Server **statusen** till **tillgänglig**

2. Om du kan se till att krypterings nyckeln har angetts på Azure Portal, krypteras data med hjälp av den kund nyckel som används i Azure Portal.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Översikt över åtkomst princip":::

### <a name="from-cli"></a>Från CLI

1. Vi kan använda *AZ CLI* -kommandot för att verifiera de nyckel resurser som används för Azure Database for PostgreSQL-servern.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    För en server utan data krypterings uppsättning resulterar det här kommandot i tom uppsättning [].

### <a name="azure-audit-reports"></a>Azure audit-rapporter

[Gransknings rapporter](https://servicetrust.microsoft.com) kan också granskas som innehåller information om efterlevnaden av data skydds standarder och föreskrifter för regler.

## <a name="next-steps"></a>Nästa steg

Läs mer om data kryptering i [Azure Database for PostgreSQL data kryptering med en server med kundhanterad nyckel](concepts-data-encryption-postgresql.md).