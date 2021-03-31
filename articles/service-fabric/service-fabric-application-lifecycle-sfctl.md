---
title: Hantera Azure Service Fabric-program med sfctl
description: Lär dig hur du distribuerar och tar bort program från ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric CLI
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711042"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Hantera ett Azure Service Fabric-program med hjälp av Azure Service Fabric CLI (sfctl)

Lär dig hur du skapar och tar bort program som körs i ett Azure Service Fabric-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Installera Service Fabric CLI. Välj sedan ditt Service Fabric-kluster. Mer information finns i [Kom igång med Service Fabric CLI](service-fabric-cli.md).

* Ha ett Service Fabric-programpaket klart att distribueras. Mer information om hur du skapar och paketerar ett program finns i [Service Fabric program modell](service-fabric-application-model.md).

## <a name="overview"></a>Översikt

Slutför följande steg för att distribuera ett nytt program:

1. Ladda upp ett programpaket till avbildnings arkivet i Service Fabric.
2. Etablera en program typ.
3. Ta bort innehållet i bild arkivet.
4. Ange och skapa ett program.
5. Ange och skapa tjänster.

Slutför följande steg för att ta bort ett befintligt program:

1. Ta bort programmet.
2. Avetablera den associerade program typen.

## <a name="deploy-a-new-application"></a>Distribuera ett nytt program

Utför följande uppgifter för att distribuera ett nytt program:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Ladda upp ett nytt programpaket till avbildnings arkivet

Innan du skapar ett program laddar du upp programpaketet till avbildnings lagringen Service Fabric.

Om ditt programpaket till exempel finns i `app_package_dir` katalogen använder du följande kommandon för att ladda upp katalogen:

```shell
sfctl application upload --path ~/app_package_dir
```

För stora programpaket kan du ange `--show-progress` alternativet för att visa förloppet för uppladdningen.

### <a name="provision-the-application-type"></a>Etablera program typen

När uppladdningen är färdig etablerar du programmet. Använd följande kommando för att etablera programmet:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Värdet för `application-type-build-path` är namnet på den katalog där du laddade upp ditt programpaket.

### <a name="delete-the-application-package"></a>Ta bort programpaketet

Vi rekommenderar att du tar bort applikations paketet när programmet har registrerats.  Om du tar bort program paket från avbildnings arkivet frigörs system resurser.  Att behålla oanvända programpaket använder disk lagring och leder till problem med program prestanda. 

Om du vill ta bort programpaketet från avbildnings arkivet använder du följande kommando:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` måste vara namnet på den katalog som du laddade upp när du skapade programmet.

### <a name="create-an-application-from-an-application-type"></a>Skapa ett program från en program typ

När du har etablerat programmet använder du följande kommando för att namnge och skapa ditt program:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` är det namn som du vill använda för program instansen. Du kan hämta ytterligare parametrar från det tidigare etablerade applikations manifestet.

Program namnet måste börja med prefixet `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Skapa tjänster för det nya programmet

När du har skapat ett program skapar du tjänster från programmet. I följande exempel skapar vi en ny tillstånds lös tjänst från vårt program. De tjänster som du kan skapa från ett program definieras i ett tjänst manifest i det tidigare etablerade programpaketet.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verifiera program distribution och hälsa

Använd följande hälso kommandon för att kontrol lera att allt är felfritt:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Kontrol lera att tjänsten är felfri genom att använda liknande kommandon för att hämta hälso tillståndet för både tjänsten och programmet:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Felfria tjänster och program har `HealthState` värdet `Ok` .

## <a name="remove-an-existing-application"></a>Ta bort ett befintligt program

Om du vill ta bort ett program utför du följande aktiviteter:

### <a name="delete-the-application"></a>Ta bort programmet

Om du vill ta bort programmet använder du följande kommando:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Avetablera program typen

När du har tagit bort programmet kan du avetablera program typen om du inte längre behöver den. Om du vill avetablera program typen använder du följande kommando:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Typ namnet och typ versionen måste matcha namnet och versionen i det tidigare etablerade applikations manifestet.

## <a name="upgrade-application"></a>Uppgradera program

När du har skapat programmet kan du upprepa samma uppsättning steg för att etablera en andra version av programmet. Med en Service Fabric program uppgradering kan du gå över till att köra den andra versionen av programmet. Mer information finns i dokumentationen om [Service Fabric program uppgraderingar](service-fabric-application-upgrade.md).

För att utföra en uppgradering måste du först etablera nästa version av programmet med samma kommandon som innan:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Vi rekommenderar att du utför en övervakad automatisk uppgradering och startar uppgraderingen genom att köra följande kommando:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uppgraderingar åsidosätter befintliga parametrar med vilken uppsättning som anges. Program parametrarna ska skickas som argument till uppgraderings kommandot vid behov. Program parametrarna ska vara kodade som JSON-objekt.

Om du vill hämta de parametrar som angetts tidigare kan du använda `sfctl application info` kommandot.

När en program uppgradering pågår kan statusen hämtas med hjälp av `sfctl application upgrade-status` kommandot.

Slutligen, om en uppgradering pågår och måste avbrytas, kan du använda `sfctl application upgrade-rollback` för att återställa uppgraderingen.

## <a name="next-steps"></a>Nästa steg

* [Grunderna för Service Fabric CLI](service-fabric-cli.md)
* [Komma igång med Service Fabric på Linux](service-fabric-get-started-linux.md)
* [Starta en Service Fabric program uppgradering](service-fabric-application-upgrade.md)
