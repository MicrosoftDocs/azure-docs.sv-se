---
title: Hämta sensor data från partner
description: Den här artikeln beskriver hur du hämtar sensor data från partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 08099ede82f8ebd9c1cd6e585a16800ee1626f65
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508795"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Hämta sensor data från sensor partner

Azure FarmBeats hjälper dig att ta strömma data från dina IoT-enheter och sensorer till Datahub. För närvarande stöds följande sensor enhets partner.

  ![FarmBeats-partner](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Genom att integrera enhets data med Azure FarmBeats kan du hämta grunddata från IoT-sensorer som distribuerats i Server gruppen till datahubben. Data, när de är tillgängliga, kan visualiseras genom FarmBeats-acceleratorn. Data kan användas för data fusion och Machine Learning/artificiell intelligens (ML/AI) modell uppbyggnad med hjälp av FarmBeats.

För att starta strömning av sensor data kontrollerar du följande:

-  Du har installerat FarmBeats på Azure Marketplace.
-  Du beslutade de sensorer och enheter som du vill installera i Server gruppen.
-  Om du planerar att använda jord fuktighets sensorer använder du FarmBeats mark fuktighets sensor karta för att få en rekommendation om antalet sensorer och var exakt du ska placera dem. Mer information finns i [generera kartor](generate-maps-in-azure-farmbeats.md).
- Du köper och distribuerar enheter eller sensorer från din enhets partner på din server grupp. Se till att du har åtkomst till sensor data via din enhets partner lösning.

## <a name="enable-device-integration-with-farmbeats"></a>Aktivera enhets integrering med FarmBeats

När du har startat strömningen av sensor data kan du påbörja processen med att hämta data till FarmBeats-systemet. Ange följande information för din enhets leverantör så att du kan integrera med FarmBeats:

 - API-slutpunkt
 - Klientorganisations-ID
 - Klient-ID
 - Klienthemlighet
 - EventHub-anslutningssträng

Följ stegen nedan för att generera ovanstående information:

> [!NOTE]
> De här stegen måste utföras på Azure för att få åtkomst till Azure-prenumerationen där FarmBeats distribueras.

1. Logga in på https://portal.azure.com/.

2. **Om du använder FarmBeats version 1.2.7 eller senare hoppar du över steg a, b och c och går till steg 3.** Du kan kontrol lera FarmBeats-versionen genom att välja **inställnings** ikonen i det övre högra hörnet av FARMBEATS-användargränssnittet.

      a.  Gå till **Azure Active Directory**  >  **app-registreringar**

      b. Välj den **app-registrering** som skapades som en del av din FarmBeats-distribution. Det får samma namn som din FarmBeats-Datahub.

      c. Välj **exponera ett API** > Välj **Lägg till ett klient program** och ange **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** och kontrol lera **auktoriserat omfång**. Detta ger åtkomst till Azure CLI (Cloud Shell) för att utföra stegen nedan:

3. Öppna Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet av Azure Portal.

    ![Azure Portal verktygsfält](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Se till att miljön är inställd på **PowerShell**. Som standard är den inställd på bash.

    ![Inställning för PowerShell-verktygsfält](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Gå till din Hem Katalog.

    ```azurepowershell-interactive
    cd
    ```

6. Kör följande kommando. Detta ansluter ett autentiserat konto som ska användas för Azure AD-begäranden

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Kör följande kommando. Detta laddar ned ett skript till din Hem Katalog.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Kör följande skript. Skriptet frågar efter klient-ID, som kan hämtas från sidan **Azure Active Directory**  >  **Översikt** .

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. Följ anvisningarna på skärmen för att samla in värdena för **API-slutpunkt**, klient-ID, **klient-ID**, **klient hemlighet**och EventHub **-** **anslutningssträng**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrera enhets data med hjälp av de genererade autentiseringsuppgifterna

Nu har du följande information som genereras från föregående avsnitt.
 - API-slutpunkt
 - EventHub-anslutningssträng
 - Klient-ID
 - Klienthemlighet
 - Klientorganisations-ID

Du måste ange detta till din enhets partner för att kunna länka FarmBeats. Gå till enhets partner portalen för att göra samma sak. Om du till exempel använder enheter från Davis instrument, Teralytic-eller Pessl-instrument (Metos.at) går du till motsvarande sidor som anges nedan:

1. [Davis instrument](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl instrument](https://ng.fieldclimate.com/user-api-services)

Enhets leverantören bekräftar en lyckad integrering. Vid bekräftelse kan du Visa alla enheter och sensorer på Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Visa enheter och sensorer

Använd följande avsnitt om du vill visa enheter och sensorer för Server gruppen.

### <a name="view-devices"></a>Visa enheter

För närvarande stöder FarmBeats följande enheter:

- **Nod**: en enhet som en eller flera sensorer är kopplade till.
- **Gateway**: en enhet som en eller flera noder är kopplade till.

Följ de här stegen:

1. På sidan start väljer du **enheter** på menyn.
  Sidan **enheter** visar enhets typ, modell, status, den server grupp som den placerats i och senaste uppdaterade datum för metadata. Som standard har kolumnen Server grupp värdet *Null*. Du kan välja att tilldela en enhet till en Server grupp. Mer information finns i [tilldela enheter](#assign-devices).
2. Välj enhet för att Visa enhets egenskaper, telemetri och underordnade enheter som är anslutna till enheten.

    ![Sidan enheter](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Visa sensorer

Följ de här stegen:

1. På Start sidan väljer du **sensorer** på menyn.
  På sidan **sensorer** visas information om typen av sensor, Server grupp den är ansluten till, överordnad enhet, portnamn, porttyp och senaste uppdaterade status.
2. Välj sensor för att Visa sensor egenskaper, aktiva aviseringar och telemetri från sensorn.

    ![Sidan sensorer](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Tilldela enheter

När sensor data flödar in kan du tilldela den till Server gruppen där du har distribuerat sensorer.

1. På sidan start väljer du **Server grupper** på menyn. Sidan **Server** grupps lista visas.
2. Välj den server grupp som du vill tilldela enheten till och välj sedan **Lägg till enheter**.
3. Fönstret **Lägg till enheter** visas. Välj den enhet som du vill tilldela till Server gruppen.

    ![Fönstret Lägg till enheter](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Välj **Lägg till enheter**. Du kan också gå till menyn **enheter** , markera de enheter som du vill tilldela till en Server grupp och välja **Associera enheter**.
5. I fönstret **Associera enheter** väljer du gruppen i list rutan och väljer **tillämpa på alla** för att associera Server gruppen till alla valda enheter.

    ![Fönstret associera enheter](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Om du vill koppla varje enhet till en annan server grupp väljer du listpilen i kolumnen **tilldela till grupp** och väljer en Server grupp för varje enhets rad.

7. Välj **tilldela** för att slutföra tilldelningen av enheten.

### <a name="visualize-sensor-data"></a>Visualisera sensordata

Följ de här stegen:

1. På sidan start väljer du **Server grupper** på menyn för att visa sidan **grupper** .
2. Välj den **Server grupp** som du vill visa sensor data för.
3. På instrument panelen för **Server gruppen** kan du Visa telemetridata. Du kan visa Live-telemetri eller använda det **anpassade intervallet** för att se ett visst datum intervall.

    ![Instrument panel för Server gruppen](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Ta bort en sensor

Följ de här stegen:

1. På Start sidan väljer du **sensorer** på menyn för att visa sidan **sensorer** .
2. Välj den enhet som du vill ta bort och välj **ta bort** i bekräftelse fönstret.

    ![Knappen Ta bort](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Ett bekräftelse meddelande visas om att sensorn har tagits bort.

## <a name="delete-devices"></a>Ta bort enheter

Följ de här stegen:

1. På sidan start väljer du **enheter** på menyn för att visa sidan **enheter** .
2. Välj den enhet som du vill ta bort och välj **ta bort** i bekräftelse fönstret.

    ![Knappen Ta bort](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Nästa steg

Nu har du sensor data som flödar till din Azure FarmBeats-instans. Nu kan du lära dig hur du [genererar kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina grupper.
