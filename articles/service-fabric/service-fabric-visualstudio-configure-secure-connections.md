---
title: Konfigurera säkra Azure-Service Fabric kluster anslutningar
description: Lär dig hur du använder Visual Studio för att konfigurera säkra anslutningar som stöds av Azure Service Fabric-klustret.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008539"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurera säkra anslutningar till ett Service Fabric kluster från Visual Studio
Lär dig hur du använder Visual Studio för säker åtkomst till ett Azure Service Fabric-kluster med åtkomst kontroll principer konfigurerade.

## <a name="cluster-connection-types"></a>Kluster anslutnings typer
Två typer av anslutningar stöds av Azure Service Fabric-klustret: **icke-säkra** anslutningar och **x509-certifikatbaserade** säkra anslutningar. (För Service Fabric-kluster som finns lokalt stöds även **Windows** -och **dSTS** -autentiseringar.) Du måste konfigurera kluster anslutnings typen när klustret skapas. När den har skapats går det inte att ändra anslutnings typen.

Visual Studio Service Fabric-verktyg stöder alla autentiseringstyper för att ansluta till ett kluster för publicering. Anvisningar om hur du konfigurerar ett säkert Service Fabric kluster finns i Konfigurera [ett Service Fabric kluster från Azure Portal](service-fabric-cluster-creation-via-portal.md) .

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurera kluster anslutningar i publicerings profiler
Om du publicerar ett Service Fabric-projekt från Visual Studio använder du dialog rutan **publicera Service Fabric program** för att välja ett Azure Service Fabric-kluster. Under **anslutnings slut punkt** väljer du ett befintligt kluster under din prenumeration.

![Dialog rutan * * publicera Service Fabric program * * används för att konfigurera en Service Fabric anslutning.][publishdialog]

Dialog rutan **publicera Service Fabric program** validerar automatiskt kluster anslutningen. Logga in på ditt Azure-konto om du uppmanas till det. Om verifieringen lyckas innebär det att systemet har rätt certifikat installerade för att ansluta till klustret på ett säkert sätt eller att klustret inte är säkert. Verifierings fel kan bero på nätverks problem eller att systemet inte har kon figurer ATS korrekt för att ansluta till ett säkert kluster.

![Dialog rutan * * publicera Service Fabric program * * verifierar en befintlig, korrekt konfigurerad Service Fabric kluster anslutning.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster
1. Se till att du har åtkomst till ett klient certifikat som är förtroende för mål klustret. Certifikatet delas vanligt vis som en PFX-fil (personal information Exchange). Mer information om hur du konfigurerar servern för att bevilja åtkomst till en klient finns i Konfigurera [ett Service Fabric kluster från Azure Portal](service-fabric-cluster-creation-via-portal.md) .
2. Installera det betrodda certifikatet. Det gör du genom att dubbelklicka på. pfx-filen eller använda PowerShell-skriptet Import-PfxCertificate för att importera certifikaten. Installera certifikatet på certifikat **: \ LocalMachine\My**. Det är OK att acceptera alla standardinställningar när certifikatet importeras.
3. Välj kommandot **publicera...** på snabb menyn för projektet för att öppna dialog rutan **publicera Azure Application** och välj sedan mål klustret. Verktyget löser automatiskt anslutningen och sparar parametrarna för säker anslutning i publicerings profilen.
4. Valfritt: du kan redigera publicerings profilen för att ange en säker kluster anslutning.
   
   Eftersom du redigerar XML-filen för publicerings profilen manuellt för att ange certifikat information, måste du anteckna certifikat arkivets namn, lagrings plats och tumavtryck för certifikatet. Du måste ange dessa värden för certifikatets lagrings namn och lagrings plats. Mer information finns i [så här: Hämta tumavtrycket för ett certifikat](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .
   
   Du kan använda *ClusterConnectionParameters* -parametrarna för att ange de PowerShell-parametrar som ska användas vid anslutning till Service Fabric klustret. Giltiga parametrar är de som accepteras av Connect-ServiceFabricCluster-cmdleten. Se [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) för en lista över tillgängliga parametrar.
   
   Om du publicerar till ett fjärrkluster måste du ange lämpliga parametrar för det aktuella klustret. Följande är ett exempel på anslutning till ett icke-säkert kluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Här är ett exempel på hur du ansluter till ett x509-certifikat baserat säkra kluster:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Redigera eventuella andra nödvändiga inställningar, till exempel uppgraderings parametrar och plats för parametern fil, och publicera sedan ditt program från dialog rutan **publicera Service Fabric program** i Visual Studio.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kommer åt Service Fabric kluster finns i [visualisera klustret med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
