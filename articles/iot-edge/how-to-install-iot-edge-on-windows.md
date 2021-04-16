---
title: Installera Azure IoT Edge för Linux på Windows | Microsoft Docs
description: Azure IoT Edge installationsinstruktioner på Windows-enheter
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538429"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installera och etablera Azure IoT Edge for Linux på en Windows-enhet (förhandsversion)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Den Azure IoT Edge körningen är det som omvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter från PC-klass till industriella servrar. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet. Mer information finns i [Förstå Azure IoT Edge och dess arkitektur.](iot-edge-runtime.md)

Azure IoT Edge för Linux i Windows kan du använda Azure IoT Edge Windows-enheter med hjälp av virtuella Linux-datorer. Linux-versionen av Azure IoT Edge och alla Linux-moduler som distribueras med den körs på den virtuella datorn. Därifrån kan Windows-program, kod och IoT Edge och moduler interagera fritt med varandra.

Den här artikeln visar stegen för att konfigurera IoT Edge på en Windows-enhet. De här stegen distribuerar en virtuell Linux-dator som innehåller IoT Edge som ska köras på din Windows-enhet och etablerar sedan enheten med dess IoT Hub enhetsidentitet.

>[!NOTE]
>IoT Edge för Linux i Windows finns i [offentlig förhandsversion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
>Även IoT Edge för Linux på Windows är den rekommenderade upplevelsen för att Azure IoT Edge i en Windows-miljö, är Windows-containrar fortfarande tillgängliga. Om du föredrar att använda Windows-containrar kan du gå till guiden [om hur du installerar och hanterar Azure IoT Edge för Windows.](how-to-install-iot-edge-windows-on-windows.md)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en giltig prenumeration. Om du inte har någon [Azure-prenumeration kan](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

* En kostnadsfri nivå eller [standardnivå IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i Azure.

* En Windows-enhet med följande minimikrav:

  * Windows 10 version 1809 eller senare; version 17763 eller senare
  * Professional-, Enterprise- eller Server-versioner
  * Minsta lediga minne: 1 GB
  * Minsta lediga diskutrymme: 10 GB
  * Om du skapar en ny distribution med hjälp Windows 10 måste du aktivera Hyper-V. Mer information finns i Installera [Hyper-V på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * Om du skapar en ny distribution med Hjälp av Windows Server kontrollerar du att du installerar Hyper-V-rollen. Mer information finns i Installera [Hyper-V-rollen på Windows Server.](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  * Om du skapar en ny distribution med hjälp av en virtuell dator kontrollerar du att du konfigurerar kapslad virtualisering korrekt. Mer information finns i guiden [för kapslad virtualisering.](nested-virtualization.md)

* Åtkomst till Windows Admin Center med Azure IoT Edge för Windows Admin Center installerat:

   1. Ladda ned [Windows Admin Center installationsprogrammet.](https://aka.ms/wacdownload)

   1. Kör det nedladdade installationsprogrammet och följ installationsguidens prompter för att installera Windows Admin Center. 

   1. När det har installerats använder du en webbläsare som stöds för att öppna Windows Admin Center. Webbläsare som stöds är Microsoft Edge (Windows 10 version 1709 eller senare), Google Chrome och Microsoft Edge Insider.

   1. Vid den första Windows Admin Center uppmanas du att välja ett certifikat som ska användas. Välj **Windows Admin Center Client** som certifikat.

   1. Det är dags att installera Azure IoT Edge tillägget. Välj kugghjulsikonen längst upp till höger på Windows Admin Center instrumentpanel.

      ![Välj kugghjulsikonen längst upp till höger på instrumentpanelen för att få åtkomst till inställningarna.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. På menyn **Inställningar** går du till **Gateway** och väljer **Tillägg**.

   1. På fliken **Tillgängliga tillägg** hittar du **Azure IoT Edge** i listan över tillägg. Välj det och välj **kommandotolken** Installera ovanför listan över tillägg.

   1. När installationen är klar bör du se Azure IoT Edge i listan över installerade tillägg på fliken **Installerade** tillägg.

## <a name="choose-your-provisioning-method"></a>Välj etableringsmetod

Azure IoT Edge för Linux i Windows stöder följande etableringsmetoder:

* Manuell etablering med IoT Edge enhetens anslutningssträng. Om du vill använda den här metoden registrerar du enheten och hämtar en anslutningssträng med hjälp av stegen i [Registrera en IoT Edge-enhet i IoT Hub](how-to-register-device.md).
  * Välj autentiseringsalternativet för symmetrisk nyckel eftersom X.509-själv signerade certifikat för närvarande inte stöds av IoT Edge för Linux i Windows.
* Automatisk etablering med enhetsetableringstjänsten (DPS) och symmetriska nycklar. Läs mer om [att skapa och etablera en IoT Edge-enhet med DPS och symmetriska nycklar.](how-to-auto-provision-symmetric-keys.md)
* Automatisk etablering med DPS- och X.509-certifikat. Läs mer om [att skapa och etablera en IoT Edge med DPS- och X.509-certifikat.](how-to-auto-provision-x509-certs.md)

Manuell etablering är enklare att komma igång med några få enheter. Device Provisioning Service är användbart för etablering av många enheter.

Om du planerar att använda någon av DPS-metoderna för att etablera din enhet eller dina enheter följer du stegen i den artikel som är länkad ovan för att skapa en instans av DPS, länkar DPS-instansen till din IoT Hub och skapar en DPS-registrering. Du kan skapa en *enskild registrering* för en enskild enhet eller en *gruppregistrering* för en grupp med enheter. Mer information om registreringstyperna finns i Azure IoT Hub [Device Provisioning Service.](../iot-dps/concepts-service.md#enrollment)

## <a name="create-a-new-deployment"></a>Skapa en ny distribution

Skapa distributionen av Azure IoT Edge för Linux på Windows på målenheten.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

På Windows Admin Center startsida under listan över anslutningar visas en lokal värdanslutning som representerar den dator där du kör Windows Admin Center. Alla ytterligare servrar, datorer eller kluster som du hanterar visas också här.

Du kan använda Windows Admin Center för att installera och hantera Azure IoT Edge för Linux i Windows på antingen din lokala enhet eller fjärrhanterade enheter. I den här guiden fungerar den lokala värdanslutningen som målenhet för distribution av Azure IoT Edge för Linux i Windows.

Om du vill distribuera till en fjärransluten målenhet i stället för din lokala enhet och du inte ser önskad målenhet i listan följer du anvisningarna för att [lägga till enheten.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Första Windows Admin Center instrumentpanel med målenheten listad](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Välj **Lägg till**.

1. I fönstret **Lägg till eller skapa** resurser letar du upp **Azure IoT Edge** panelen. Välj **Skapa ny** för att installera en ny instans av Azure IoT Edge för Linux på Windows på en enhet.

   Om du redan har IoT Edge för Linux på Windows som  körs på en enhet kan du välja Lägg till för att ansluta till den befintliga IoT Edge-enheten och hantera den med Windows Admin Center.

   ![Välj Skapa ny på Azure IoT Edge panelen i Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Fönstret **Skapa en Azure IoT Edge för Linux i Windows-distributionen** öppnas. På **1. Komma igång** kontrollerar du att målenheten uppfyller minimikraven och väljer **Nästa.**

1. Granska licensvillkoren, markera **Jag accepterar** och välj **Nästa.**

1. Du kan aktivera eller **inaktivera valfria diagnostikdata** beroende på vad du föredrar.

1. Välj **Nästa: Distribuera**.

   ![Välj knappen Nästa: Distribuera när du har växlat valfria diagnostikdata till dina inställningar](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. På **2. Fliken** Distribuera under Välj **en målenhet klickar** du på enheten i listan för att verifiera att den uppfyller minimikraven. När statusen har bekräftats som stöds väljer du **Nästa.**

   ![Välj din enhet för att kontrollera att den stöds](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. På fliken **2.2 Inställningar** granskar du konfigurationsinställningarna för distributionen. När du är nöjd med inställningarna väljer du **Nästa.**

   ![Granska konfigurationsinställningarna för distributionen](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Om du använder en virtuell Windows-dator rekommenderar vi att du använder en standardväxel i stället för en extern växel för att se till att den virtuella Linux-datorn som skapades i distributionen kan hämta en IP-adress.
   >
   >Med en standardväxel tilldelas den virtuella Linux-datorn en intern IP-adress. Den här interna IP-adressen kan inte nås utanför den virtuella Windows-datorn, men den kan anslutas lokalt när den är inloggad på den virtuella Windows-datorn.
   >
   >Om du använder Windows Server bör du observera att Azure IoT Edge för Linux i Windows inte automatiskt stöder standardväxeln. För en lokal virtuell Windows Server-dator ser du till att den virtuella Linux-datorn kan hämta en IP-adress via den externa växeln. För en virtuell Windows Server-dator i Azure måste du konfigurera en intern växel innan du IoT Edge för Linux i Windows.

1. På fliken **2.3** Distribution kan du se förloppet för distributionen. Den fullständiga processen omfattar att ladda Azure IoT Edge för Linux på Windows-paketet, installera paketet, konfigurera värdenheten och konfigurera den virtuella Linux-datorn. Den här processen kan ta flera minuter att slutföra. En lyckad distribution visas nedan.

   ![Vid en lyckad distribution visas varje steg med en grön bockmarkering och en "Complete"-etikett](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

När distributionen är klar är du redo att etablera din enhet. Välj **Nästa: Anslut för** att fortsätta till **3. Fliken** Anslut, som hanterar Azure IoT Edge enhetsetablering.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installera IoT Edge för Linux i Windows på målenheten om du inte redan har gjort det.

> [!NOTE]
> Följande PowerShell-process beskriver hur du skapar en lokal värddistribution av Azure IoT Edge för Linux i Windows. Om du vill skapa en distribution till en fjärransluten målenhet med hjälp av PowerShell kan du använda [Fjärr-PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) för att upprätta en anslutning till en fjärrenhet och köra dessa kommandon via fjärranslutning på enheten.

1. I en upphöjd PowerShell-session kör du vart och ett av följande kommandon för att IoT Edge för Linux i Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installera IoT Edge linux på Windows på enheten.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Du kan ange anpassade IoT Edge för Linux på Windows-installation och VHDX-kataloger genom att lägga till parametrarna INSTALLDIR="<FULLY_QUALIFIED_PATH>" och VHDXDIR="<FULLY_QUALIFIED_PATH>" i installationskommandot ovan.

1. För att distributionen ska kunna köras måste du ange körningsprincipen på målenheten till `AllSigned` om den inte redan är det. Du kan kontrollera den aktuella körningsprincipen i en upphöjd PowerShell-kommandotolk med hjälp av:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Om körningsprincipen för `local machine` inte är kan du ange `AllSigned` körningsprincipen med hjälp av:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Skapa IoT Edge Linux i Windows-distributionen.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Du kan köra det här kommandot utan parametrar eller välja att anpassa distributionen med parametrar. Du kan referera till [IoT Edge för Linux på Windows PowerShell för](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) att se betydelser för parametrar och standardvärden.

1. Ange "Y" för att godkänna licensvillkoren.

1. Ange "O" eller "R" för att aktivera **eller** inaktivera valfria diagnostikdata, beroende på vad du föredrar. En lyckad distribution visas nedan.

   ![En lyckad distribution säger "Distributionen lyckades" i slutet av meddelandena](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

När distributionen är klar är du redo att etablera enheten.

---

Om du vill etablera din enhet kan du följa länkarna nedan för att gå till avsnittet för den valda etableringsmetoden:

* [Alternativ 1: Manuell etablering med IoT Edge-enhetens anslutningssträng](#option-1-provisioning-manually-using-the-connection-string)
* [Alternativ 2: Automatisk etablering med enhetsetableringstjänsten (DPS) och symmetriska nycklar](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Alternativ 3: Automatisk etablering med DPS- och X.509-certifikat](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Etablera din enhet

Välj en metod för att etablera enheten och följ instruktionerna i lämpligt avsnitt. Du kan använda Windows Admin Center eller en upphöjd PowerShell-session för att etablera dina enheter.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Alternativ 1: Etablering manuellt med anslutningssträngen

Det här avsnittet beskriver hur du etablerar enheten manuellt Azure IoT Edge enhetens anslutningssträng.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge för enhetsetablering** väljer du **Anslutningssträng (manuell)** i listrutan etableringsmetod.

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till **IoT Edge** på IoT Hub.

1. Klicka på enhetens enhets-ID. Kopiera fältet **Primär anslutningssträng.**

1. Klistra in den i fältet enhetsanslutningssträng i Windows Admin Center. Välj sedan **Etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har klistrat in enhetens anslutningssträng](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. När etableringen är klar väljer du **Slutför.** Du kommer tillbaka till huvudinstrumentpanelen. Nu bör du se en ny enhet i listan vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge för att ansluta till den. På **översiktssidan** kan du visa listan över **IoT Edge och visa** IoT Edge **status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till **IoT Edge** på IoT Hub.

1. Klicka på enhetens enhets-ID. Kopiera fältet **Primär anslutningssträng.**

1. Klistra in platshållartexten i följande kommando och kör den i en upphöjd PowerShell-session på målenheten.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Alternativ 2: Etablering via DPS med symmetriska nycklar

Det här avsnittet beskriver hur du etablerar enheten automatiskt med hjälp av DPS och symmetriska nycklar.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge för enhetsetablering** väljer du Symmetrisk nyckel **(DPS)** i listrutan etableringsmetod.

1. I Azure Portal [du](https://ms.portal.azure.com/)till DPS-instansen.

1. På fliken **Översikt** kopierar du värdet **ID-omfång.** Klistra in det i fältet omfångs-ID i Windows Admin Center.

1. På fliken **Hantera registreringar** i Azure Portal väljer du den registrering som du skapade. Kopiera värdet **för Primärnyckel** i registreringsinformationen. Klistra in den i fältet symmetrisk nyckel i Windows Admin Center.

1. Ange registrerings-ID för enheten i fältet registrerings-ID i Windows Admin Center.

1. Välj **Etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har fyllt i de obligatoriska fälten för etablering av symmetrisk nyckel](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. När etableringen är klar väljer du **Slutför.** Du kommer tillbaka till huvudinstrumentpanelen. Nu bör du se en ny enhet i listan vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge för att ansluta till den. På **översiktssidan** kan du visa listan över **IoT Edge och visa** IoT Edge **status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopiera följande kommando till en textredigerare. Ersätt platshållartexten med din information enligt beskrivningen.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. I Azure Portal [du](https://ms.portal.azure.com/)till DPS-instansen.

1. På fliken **Översikt** kopierar du värdet **FÖR ID-omfång.** Klistra in den över lämplig platshållartext i kommandot.

1. På fliken **Hantera registreringar** i Azure Portal väljer du den registrering som du skapade. Kopiera värdet **för Primärnyckel** i registreringsinformationen. Klistra in den över lämplig platshållartext i kommandot.

1. Ange registrerings-ID för enheten för att ersätta lämplig platshållartext i kommandot.

1. Kör kommandot i en upphöjd PowerShell-session på målenheten.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Alternativ 3: Etablering via DPS med X.509-certifikat

Det här avsnittet beskriver hur du etablerar enheten automatiskt med hjälp av DPS- och X.509-certifikat.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge för** enhetsetablering väljer du **X.509-certifikat (DPS)** i listrutan för etableringsmetod.

1. I Azure Portal [du](https://ms.portal.azure.com/)till DPS-instansen.

1. På fliken **Översikt** kopierar du värdet **FÖR ID-omfång.** Klistra in det i fältet omfångs-ID i Windows Admin Center.

1. Ange registrerings-ID för enheten i fältet registrerings-ID i Windows Admin Center.

1. Ladda upp certifikat och privata nyckelfiler.

1. Välj **Etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har fyllt i de obligatoriska fälten för X.509-certifikatetablering](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. När etableringen är klar väljer du **Slutför.** Du kommer tillbaka till huvudinstrumentpanelen. Nu bör du se en ny enhet i listan vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge för att ansluta till den. På **översiktssidan** kan du visa listan över **IoT Edge och visa** IoT Edge **status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopiera följande kommando till en textredigerare. Ersätt platshållartexten med din information enligt beskrivningen.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. I Azure Portal [du](https://ms.portal.azure.com/)till DPS-instansen.

1. På fliken **Översikt** kopierar du värdet **FÖR ID-omfång.** Klistra in den över lämplig platshållartext i kommandot.

1. Ange registrerings-ID för enheten för att ersätta lämplig platshållartext i kommandot.

1. Ersätt lämplig platshållartext med den absoluta källsökvägen till certifikatfilen.

1. Ersätt lämplig platshållartext med den absoluta källsökvägen till filen med din privata nyckel.

1. Kör kommandot i en upphöjd PowerShell-session på målenheten.

---

## <a name="verify-successful-configuration"></a>Kontrollera att konfigurationen lyckades

Kontrollera att IoT Edge för Linux på Windows har installerats och konfigurerats på din IoT Edge enhet.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Välj din IoT Edge i listan över anslutna enheter i Windows Admin Center ansluta till den.

1. På översiktssidan för enheten visas viss information om enheten:

    1. Avsnittet **IoT Edge modullista** visar moduler som körs på enheten. När IoT Edge startar för första gången bör du bara se att **edgeAgent-modulen** körs. EdgeAgent-modulen körs som standard och hjälper till att installera och starta eventuella ytterligare moduler som du distribuerar till din enhet.
    1. Avsnittet **IoT Edge Status** visar tjänststatus och bör rapportera aktiv **(körs).**

1. Om du behöver felsöka IoT Edge-tjänsten använder  du kommandogränssnittsverktyget på enhetssidan för att ssh (secure shell) till den virtuella datorn och köra Linux-kommandona.

    1. Hämta tjänstloggar om du behöver felsöka tjänsten.

       ```bash
       journalctl -u iotedge
       ```

    2. Använd verktyget `check` för att verifiera enhetens konfiguration och anslutningsstatus.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Logga in på din IoT Edge Linux på den virtuella Windows-datorn med följande kommando i PowerShell-sessionen:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Det enda konto som tillåts för SSH till den virtuella datorn är den användare som skapade den.

1. När du har loggat in kan du kontrollera listan över körningar IoT Edge moduler med hjälp av följande Linux-kommando:

   ```bash
   iotedge list
   ```

1. Om du behöver felsöka IoT Edge tjänsten använder du följande Linux-kommandon.

    1. Hämta tjänstloggar om du behöver felsöka tjänsten.

       ```bash
       journalctl -u iotedge
       ```

    2. Använd verktyget `check` för att verifiera enhetens konfiguration och anslutningsstatus.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Nästa steg

* Fortsätt att [distribuera IoT Edge moduler för](how-to-deploy-modules-portal.md) att lära dig hur du distribuerar moduler till din enhet.
* Lär dig hur du hanterar certifikat på din IoT Edge linux på en virtuell [Windows-dator](how-to-manage-device-certificates.md) och överför filer från värdoperativsystemet till din virtuella Linux-dator.
* Lär dig hur [du konfigurerar IoT Edge att kommunicera via en proxyserver.](how-to-configure-proxy-support.md)
