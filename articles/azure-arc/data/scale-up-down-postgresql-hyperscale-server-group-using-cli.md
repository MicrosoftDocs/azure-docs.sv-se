---
title: Skala upp och ned en Azure Database for PostgreSQL storskalig Server grupp med CLI (azdata eller kubectl)
description: Skala upp och ned en Azure Database for PostgreSQL storskalig Server grupp med CLI (azdata eller kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687557"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Skala upp och ned en Azure Database for PostgreSQL storskalig Server grupp med CLI (azdata eller kubectl)



Det finns tillfällen när du kan behöva ändra egenskaperna eller definitionen för en Server grupp. Exempel:

- Skala upp eller ned antalet virtuella kärnor som var och en av koordinatorn eller arbets noderna använder
- Skala upp eller ned minnet som var och en av koordinatorn eller arbets noderna använder

Den här guiden förklarar hur du skalar vCore och/eller minne.

Om du skalar upp eller ned vCore-eller minnes inställningarna för Server gruppen innebär det att du har möjlighet att ange minst och/eller maximalt för var och en av inställningarna för vCore och minne. Om du vill konfigurera server gruppen att använda ett visst antal vCore eller en speciell mängd minne, anger du de lägsta inställningarna som motsvarar Max inställningarna.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Visa den aktuella definitionen av Server gruppen

Om du vill visa den aktuella definitionen av Server gruppen och se vilka som är aktuella inställningar för vCore och minne, kör du något av följande kommando:

### <a name="cli-with-azdata"></a>CLI med azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI med kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Om du har skapat en Server grupp av PostgreSQL version 11 kör du `kubectl describe postgresql-11/<server group name>` i stället.

Den returnerar konfigurationen för Server gruppen. Om du har skapat Server gruppen med standardinställningarna bör du se definitionen på följande sätt:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Tolka definitionen av Server gruppen

I definitionen av en Server grupp är avsnittet som uppfyller inställningarna för min/högsta-vCore per nod och minsta/högsta minne per nod den **"schemaläggning"** -avsnittet. I det avsnittet sparas Max inställningarna i ett underavsnitt som kallas **"gränser"** och de lägsta inställningarna sparas i underavsnittet **"requests**".

Om du anger inställningar för min som skiljer sig från Max inställningarna garanterar konfigurationen att Server gruppen allokeras till de begärda resurserna om den behövs. Det kommer inte att överskrida de gränser som du anger.

De resurser (virtuella kärnor och minne) som faktiskt kommer att användas av Server gruppen är upp till de högsta inställningarna och är beroende av arbets belastningarna och de resurser som är tillgängliga i klustret. Om du inte lägger till inställningarna med max, kan Server gruppen använda upp till alla resurser som Kubernetes-klustret allokerar till de Kubernetes-noder som server gruppen har schemalagts för.

Dessa inställningar för vCore och minne gäller för var och en av PostgreSQL-noderna (koordinator-noden och arbetsnoder). Det finns ännu inte stöd för att ange definitioner för koordinator-noden och arbetsnoderna separat.

I en standard konfiguration är bara det minsta minnet som är inställt på 256Mi eftersom det är den minsta mängd minne som rekommenderas för att köra PostgreSQL-skalning.

> [!NOTE]
> Att ställa in ett minimum innebär inte att Server gruppen kommer att använda det minimum. Det innebär att om Server gruppen behöver den, är det garanterat att den tilldelas minst detta minimi krav. Anta till exempel att vi har ställt in `--minCpu 2` . Det innebär inte att Server gruppen kommer att använda minst 2 virtuella kärnor hela tiden. Det innebär i stället att gruppen kan börja använda mindre än 2 virtuella kärnor om den inte behöver det så mycket och att den garanterat tilldelas minst 2 virtuella kärnor om den behöver dem senare. Det innebär att Kubernetes-klustret allokerar resurser till andra arbets belastningar på ett sådant sätt att den kan allokera 2 virtuella kärnor till Server gruppen om den någonsin behöver dem.

>[!NOTE]
>Innan du ändrar konfigurationen av systemet måste du bekanta dig med [resurs modellen Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>Skala upp Server gruppen

De inställningar som du håller på att ställa in måste beaktas i konfigurationen som du har angett för ditt Kubernetes-kluster. Se till att du inte anger värden som ditt Kubernetes-kluster inte kan uppfylla. Detta kan leda till fel eller oförutsägbara beteenden. Exempel: om status för Server gruppen fortfarande är i status _uppdatering_ under en längre tid efter att du har ändrat konfigurationen, kan det vara en indikation att du ställer in parametrarna nedan till värden som ditt Kubernetes-kluster inte kan uppfylla. Om så är fallet, återställer du ändringen eller läser _troubleshooting_section.

Exempel: anta att du vill skala upp definitionen av Server gruppen till:

- Minsta vCore = 2
- Max vCore = 4
- Minsta minne = 512 MB
- Maximalt minne = 1 GB

Du använder någon av följande metoder:

### <a name="cli-with-azdata"></a>CLI med azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Nedan visas ett exempel som visar hur du kan använda kommandot. Innan du kör ett Edit-kommando måste du ange parametrarna till värden som Kubernetes-klustret kan hantera.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

Kommandot körs korrekt när det visas:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Om du vill ha mer information om parametrarna kör du `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>CLI med kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Detta tar dig i vi-redigeraren där du kan navigera och ändra konfigurationen. Använd följande för att mappa den önskade inställningen till namnet på fältet i specifikationen:

> [!CAUTION]
> Nedan visas ett exempel som illustrerar hur du kan redigera konfigurationen. Innan du uppdaterar konfigurationen måste du ange parametrarna till värden som Kubernetes-klustret kan hantera.

Exempel:
- Min vCore = 2-> scheduling\default\resources\requests\cpu
- Max vCore = 4-> scheduling\default\resources\limits\cpu
- Minsta minne = 512 MB-> scheduling\default\resources\requests\cpu
- Högsta mängd minne = 1 GB-> scheduling\default\resources\limits\cpu

Om du inte är bekant med vi-redigeraren, se en beskrivning av de kommandon som du kan behöva [här](https://www.computerhope.com/unix/uvi.htm):
- redigerings läge: `i`
- flytta runt med pilar
- _stop redigering: `esc`
- _exit utan att spara: `:qa!`
- _exit efter att ha sparat: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Visa den skalade definitionen av Server gruppen

Kör kommandot igen för att visa definitionen av Server gruppen och kontrol lera att den är angiven som du vill:

### <a name="cli-with-azdata"></a>CLI med azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI med kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Om du har skapat en Server grupp av PostgreSQL version 11 kör du `kubectl describe postgresql-11/<server group name>` i stället.


Den nya definitionen av Server gruppen visas:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Skala ned Server gruppen

För att skala ned Server gruppen som du kör samma kommando, men ange lägre värden för de inställningar som du vill skala ned. Om du vill ta bort begär Anden och/eller gränser anger du dess värde som tom sträng.

## <a name="reset-to-default-values"></a>Återställ till standardvärden
Om du vill återställa kärn-/minnes gränser/begär ande parametrar till standardvärdena redigerar du dem och skickar en tom sträng i stället för ett faktiskt värde. Om du till exempel vill återställa parametern för kärn gränsen (cl) kör du följande kommandon:
- på en Linux-klient:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- på en Windows-klient: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>Nästa steg

- [Skala ut din Azure Database for PostgreSQL Hyperscale-servergrupp](scale-out-postgresql-hyperscale-server-group.md)
- [Lagrings konfiguration och Kubernetes lagrings koncept](storage-configuration.md)
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
