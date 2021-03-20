---
title: Vanliga orsaker till moln tjänst (klassisk) åter användning av roller | Microsoft Docs
description: En moln tjänst roll som plötsligt återanvänds kan orsaka betydande stillestånds tid. Här följer några vanliga problem som gör att roller återvinns, vilket kan hjälpa dig att minska stillestånds tiden.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9610b32207f8367b9415c0881e49b54e24c49ad7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741170"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Vanliga problem som gör att Azure Cloud Service (klassiska)-roller kan återanvändas

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln beskriver några vanliga orsaker till distributions problem och innehåller fel söknings tips som hjälper dig att lösa problemen. En indikation på att ett problem har uppstått med ett program är när roll instansen inte startar, eller så växlar den mellan initierings-, upptaget-och stopp tillstånd.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Saknar körningsberoenden
Om en roll i programmet är beroende av en sammansättning som inte är en del av .NET Framework eller det hanterade Azure-biblioteket, måste du uttryckligen ta med den sammansättningen i programpaketet. Tänk på att andra Microsoft-ramverk inte är tillgängliga på Azure som standard. Om rollen är beroende av ett sådant ramverk måste du lägga till dessa paket i programpaketet.

Innan du skapar och paketerar programmet kontrollerar du följande:

* Om du använder Visual Studio kontrollerar du att egenskapen **Kopiera lokal** har värdet **True** för varje refererad sammansättning i projektet som inte är en del av Azure SDK eller .NET Framework.
* Kontrol lera att web.configs filen inte refererar till oanvända sammansättningar i elementet Compilation.
* **Build-åtgärden** för varje. cshtml-fil har angetts till **Content**. Detta säkerställer att filerna visas korrekt i paketet och gör att andra refererade filer kan visas i paketet.

## <a name="assembly-targets-wrong-platform"></a>Monterings mål fel plattform
Azure är en 64-bitars miljö. Därför fungerar inte .NET-sammansättningar som kompileras för ett 32-bitars mål i Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rollen genererar ohanterade undantag vid initiering eller stopp
Undantag som har utlösts av metoderna i [RoleEntryPoint] -klassen, som innehåller metoderna [OnStart], [OnStop]och [Run] , är ohanterade undantag. Om ett ohanterat undantag uppstår i någon av dessa metoder kommer rollen att återvinna. Om rollen åter användning görs upprepade gånger kan det leda till ett ohanterat undantag varje gång det försöker starta.

## <a name="role-returns-from-run-method"></a>Rollen returnerar från körnings metoden
[Körnings] metoden är avsedd att köras oändligt. Om din kod åsidosätter [körnings] metoden bör den vila i låg tid. Om [körnings] metoden returnerar återvinner rollen.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Felaktig DiagnosticsConnectionString-inställning
Om programmet använder Azure-diagnostik måste tjänst konfigurations filen ange `DiagnosticsConnectionString` konfigurations inställningen. Den här inställningen ska ange en HTTPS-anslutning till ditt lagrings konto i Azure.

Kontrol lera följande för att se till att `DiagnosticsConnectionString` inställningen är korrekt innan du distribuerar ditt programpaket till Azure:  

* `DiagnosticsConnectionString`Inställningen pekar på ett giltigt lagrings konto i Azure.  
  Som standard pekar den här inställningen på det emulerade lagrings kontot, så du måste uttryckligen ändra den här inställningen innan du distribuerar ditt programpaket. Om du inte ändrar den här inställningen uppstår ett undantags fel när roll instansen försöker starta den diagnostiska övervakaren. Detta kan leda till att roll instansen omåtervinns oändligt.
* Anslutnings strängen har angetts i följande [format](../storage/common/storage-configure-connection-string.md). (Protokollet måste anges som HTTPS.) Ersätt *MyAccountName* med namnet på ditt lagrings konto och *MyAccountKey* med din åtkomst nyckel:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Om du utvecklar ditt program med hjälp av Azure Tools för Microsoft Visual Studio kan du använda egenskaps sidorna för att ange det här värdet.

## <a name="exported-certificate-does-not-include-private-key"></a>Det exporterade certifikatet innehåller inte privat nyckel
Om du vill köra en webb roll under TLS måste du se till att det exporterade hanterings certifikatet innehåller den privata nyckeln. Om du använder *Windows certifikat hanteraren* för att exportera certifikatet måste du välja **Ja** för alternativet **Exportera den privata nyckeln** . Certifikatet måste exporteras i PFX-format, vilket är det enda format som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg
Visa fler [fel söknings artiklar](../index.yml?product=cloud-services&tag=top-support-issue) för Cloud Services.

Visa fler scenarier för roll åter användning i [Kevin Williamsons blogg serie](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Kör]: /previous-versions/azure/reference/ee772746(v=azure.100)