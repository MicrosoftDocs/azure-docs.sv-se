---
title: Installera Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner på Windows-enheter
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b549d868aed443e19d639ba6f6df7db20e014b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612132"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installera och etablera Azure IoT Edge for Linux på en Windows-enhet (förhandsversion)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter från dator klass till industriella servrar. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogiken till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Azure IoT Edge för Linux i Windows kan du använda Azure IoT Edge på Windows-enheter med hjälp av virtuella Linux-datorer. Linux-versionen av Azure IoT Edge och eventuella Linux-moduler som distribueras med den körs på den virtuella datorn. Därifrån kan Windows-program och-kod och IoT Edge Runtime och moduler interagera fritt med varandra.

I den här artikeln beskrivs stegen för att ställa in IoT Edge på en Windows-enhet. De här stegen distribuerar en virtuell Linux-dator som innehåller IoT Edge runtime som ska köras på din Windows-enhet och etablerar sedan enheten med enhetens IoT Hub enhets identitet.

>[!NOTE]
>IoT Edge för Linux på Windows finns i en [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Även om IoT Edge för Linux i Windows är den rekommenderade upplevelsen för att använda Azure IoT Edge i en Windows-miljö är Windows-behållare fortfarande tillgängliga. Om du föredrar att använda Windows-behållare kan du läsa instruktionen för att [Installera och hantera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en giltig prenumeration. Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

* En kostnads fri eller standard nivå [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i Azure.

* En Windows-enhet med följande minsta system krav:

  * Windows 10 version 1809 eller senare; Build 17763 eller senare
  * Professional-, Enterprise-eller Server-versioner
  * Minsta lediga minne: 2 GB
  * Minsta lediga disk utrymme: 10 GB
  * Om du skapar en ny distribution med Windows 10 kontrollerar du att du aktiverar Hyper-V. Mer information finns i så här [installerar du Hyper-V på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md).
  * Om du skapar en ny distribution med hjälp av Windows Server måste du kontrol lera att du har installerat Hyper-V-rollen. Mer information finns i så här [installerar du Hyper-V-rollen på Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md).
  * Om du skapar en ny distribution med en virtuell dator kontrollerar du att du konfigurerar kapslad virtualisering på rätt sätt. Mer information finns i den [kapslade Virtualization](nested-virtualization.md) -guiden.

* Åtkomst till Windows administrations Center med Azure IoT Edge tillägget för Windows administrations Center installerat:

   1. Hämta [installations programmet för Windows administrations Center](https://aka.ms/wacdownload).

   1. Kör det nedladdade installations programmet och följ anvisningarna i installations guiden för att installera Windows administrations Center. 

   1. När du har installerat kan du använda en webbläsare som stöds för att öppna administrations Center för Windows. Webbläsare som stöds är Microsoft Edge (Windows 10, version 1709 eller senare), Google Chrome och Microsoft Edge Insider.

   1. Vid den första användningen av Windows administrations Center uppmanas du att välja ett certifikat som ska användas. Välj **Windows administrations Center-klient** som certifikat.

   1. Det är dags att installera Azure IoT Edge-tillägget. Välj kugg hjuls ikonen längst upp till höger på instrument panelen i Windows administrations Center.

      ![Välj kugg hjuls ikonen längst upp till höger på instrument panelen för att få åtkomst till inställningarna.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Välj **tillägg** under **Gateway** på menyn **Inställningar** .

   1. På fliken **tillgängliga tillägg** söker du efter **Azure IoT Edge** i listan över tillägg. Välj den och välj **installations** meddelandet ovanför listan med tillägg.

   1. När installationen är klar bör du se Azure IoT Edge i listan över installerade tillägg på fliken **installerade tillägg** .

## <a name="choose-your-provisioning-method"></a>Välj etablerings metod

Azure IoT Edge för Linux på Windows stöder följande etablerings metoder:

* Manuell etablering med hjälp av din IoT Edge enhets anslutnings sträng. Om du vill använda den här metoden registrerar du enheten och hämtar en anslutnings sträng med hjälp av stegen i [Registrera en IoT Edge enhet i IoT Hub](how-to-register-device.md).
  * Välj alternativet för symmetrisk nyckel autentisering, som X. 509 självsignerade certifikat stöds inte för närvarande av IoT Edge för Linux i Windows.
* Automatisk etablering med hjälp av enhets etablerings tjänsten (DPS) och symmetriska nycklar. Lär dig mer om att [skapa och tillhandahålla en IoT Edge enhet med DPS och symmetriska nycklar](how-to-auto-provision-symmetric-keys.md).
* Automatisk etablering med hjälp av DPS-och X. 509-certifikat. Lär dig mer om att [skapa och tillhandahålla en IoT Edge-enhet med DPS-och X. 509-certifikat](how-to-auto-provision-x509-certs.md).

Manuell etablering är enklare att komma igång med några få enheter. Enhets etablerings tjänsten är användbar för att tillhandahålla många enheter.

Om du planerar att använda en av DPS-metoderna för att etablera enheten eller enheterna följer du stegen i lämplig artikel som är länkad ovan för att skapa en instansen av DPS, länkar din DPS-instans till din IoT Hub och skapar en DPS-registrering. Du kan skapa en *enskild registrering* för en enskild enhet eller en *grupp registrering* för en grupp av enheter. Mer information om registrerings typerna finns i [Azure IoT Hub Device Provisioning service Concepts](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Skapa en ny distribution

Skapa din distribution av Azure IoT Edge för Linux i Windows på mål enheten.

# <a name="windows-admin-center"></a>[Windows administrations Center](#tab/windowsadmincenter)

På Start sidan i Windows administrations Center, under listan över anslutningar, visas en lokal värd anslutning som representerar den dator där du kör Windows administrations Center. Eventuella ytterligare servrar, datorer eller kluster som du hanterar visas också här.

Du kan använda Windows administrations Center för att installera och hantera Azure IoT Edge för Linux i Windows på en lokal enhet eller fjärrhanterade enheter. I den här hand boken fungerar den lokala värd anslutningen som mål enheten för distributionen av Azure IoT Edge för Linux i Windows.

Om du vill distribuera till en fjär renhet i stället för den lokala enheten och du inte ser den önskade mål enheten i listan, följer du [anvisningarna för att lägga till din enhet.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Första instrument panel för Windows administrations Center med mål enheten listad](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Välj **Lägg till**.

1. Leta upp panelen **Azure IoT Edge** i fönstret **Lägg till eller skapa resurser** . Välj **Skapa ny** för att installera en ny instans av Azure IoT Edge för Linux i Windows på en enhet.

   Om du redan har IoT Edge för Linux i Windows som körs på en enhet kan du välja **Lägg till** för att ansluta till den befintliga IoT Edge enheten och hantera den med Windows administrations Center.

   ![Välj Skapa ny på Azure IoT Edge panelen i Windows administrations Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. **Distributions fönstret Skapa en Azure IoT Edge för Linux i Windows** öppnas. På **1.** På fliken komma igång kontrollerar du att mål enheten uppfyller minimi kraven och väljer **sedan nästa**.

1. Läs igenom licens villkoren, markera **Jag accepterar** och välj **Nästa**.

1. Du kan växla mellan **valfria diagnostikdata** på eller av, beroende på vad du föredrar.

1. Välj **Nästa: Distribuera**.

   ![Välj Nästa: knappen distribuera när du har växlat valfria diagnostikdata till din preferens](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. På **2. Distribuera** fliken, under **Välj en målenhet**, klickar du på enheten i listan för att kontrol lera att den uppfyller minimi kraven. När dess status har bekräftats som stöds väljer du **Nästa**.

   ![Välj din enhet för att kontrol lera att den stöds](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Granska konfigurations inställningarna för distributionen på fliken **2,2 inställningar** . När du är nöjd med inställningarna väljer du **Nästa**.

   ![Granska konfigurations inställningarna för distributionen](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Om du använder en virtuell Windows-dator rekommenderar vi att du använder en standard växel i stället för en extern växel för att se till att den virtuella Linux-datorn som skapas i distributionen kan erhålla en IP-adress.
   >
   >Med en standard växel tilldelas den virtuella Linux-datorn en intern IP-adress. Den här interna IP-adressen kan inte nås utanför den virtuella Windows-datorn, men den kan vara ansluten till lokalt när du är inloggad på den virtuella Windows-datorn.
   >
   >Observera att Azure IoT Edge för Linux i Windows inte automatiskt stöder standard växeln om du använder Windows Server. För en lokal virtuell Windows Server-dator kan du se till att den virtuella Linux-datorn kan erhålla en IP-adress via den externa växeln. För en virtuell Windows Server-dator i Azure konfigurerar du en intern växel innan du distribuerar IoT Edge för Linux i Windows.

1. På fliken **2,3 distribution** kan du se förloppet för distributionen. Den fullständiga processen omfattar att ladda ned Azure IoT Edge för Linux på Windows-paket, installera paketet, konfigurera värd enheten och konfigurera den virtuella Linux-datorn. Det kan ta flera minuter att slutföra den här processen. En lyckad distribution visas i bilden nedan.

   ![Vid en lyckad distribution visas varje steg med en grön bock markering och en fullständig etikett](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

När distributionen är klar är du redo att etablera enheten. Välj **Nästa: Anslut** för att gå vidare till **3. Fliken Anslut** , som hanterar Azure IoT Edge enhets etablering.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installera IoT Edge för Linux på Windows på mål enheten om du inte redan har gjort det.

> [!NOTE]
> Följande PowerShell-process beskriver hur du skapar en lokal värd distribution av Azure IoT Edge för Linux i Windows. Om du vill skapa en distribution till en fjärran sluten mål enhet med hjälp av PowerShell kan du använda [fjärr-PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) för att upprätta en anslutning till en fjärran sluten enhet och köra kommandona på distans på enheten.

1. Kör följande kommandon i en upphöjd PowerShell-session för att ladda ned IoT Edge för Linux i Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installera IoT Edge för Linux i Windows på din enhet.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Du kan ange anpassade IoT Edge för Linux på Windows-installation och VHDX-kataloger genom att lägga till parametrarna INSTALLDIR = "<FULLY_QUALIFIED_PATH>" och VHDXDIR = "<FULLY_QUALIFIED_PATH>" i installations kommandot ovan.

1. För att distributionen ska kunna köras måste du ställa in körnings principen på mål enheten på `AllSigned` om den inte redan finns. Du kan kontrol lera den aktuella körnings principen i en upphöjd PowerShell-prompt med:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Om körnings principen för `local machine` inte är det `AllSigned` kan du ange körnings principen med hjälp av:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Skapa IoT Edge för Linux på Windows-distribution.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Du kan köra det här kommandot utan parametrar eller anpassa distributionen med parametrar om du vill. Du kan referera till [referensen IoT Edge för Linux på Windows PowerShell-skript](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) för att se parameter betydelser och standardvärden.

1. Ange "Y" om du vill acceptera licens villkoren.

1. Ange "O" eller "R" om du vill växla mellan **valfria diagnostikdata** , beroende på vad du föredrar. En lyckad distribution visas i bilden nedan.

   ![En lyckad distribution säger att distributionen lyckades i slutet av meddelandena](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

När distributionen är klar är du redo att etablera enheten.

---

Du kan etablera din enhet genom att följa länkarna nedan och gå till avsnittet för din valda etablerings metod:

* [Alternativ 1: manuell etablering med hjälp av din IoT Edge enhets anslutnings sträng](#option-1-provisioning-manually-using-the-connection-string)
* [Alternativ 2: automatisk etablering med hjälp av enhets etablerings tjänsten (DPS) och symmetriska nycklar](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Alternativ 3: automatisk etablering med DPS-och X. 509-certifikat](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Etablera din enhet

Välj en metod för att konfigurera enheten och följ instruktionerna i lämpligt avsnitt. Du kan använda Windows administrations Center eller en upphöjd PowerShell-session för att etablera dina enheter.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Alternativ 1: etablering manuellt med anslutnings strängen

I det här avsnittet beskrivs hur du konfigurerar enheten manuellt med hjälp av din Azure IoT Edge enhets anslutnings sträng.

# <a name="windows-admin-center"></a>[Windows administrations Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge enhets etablering** väljer du **anslutnings sträng (manuell)** i list rutan etablerings metod.

1. I [Azure Portal](https://ms.portal.azure.com/)går du till fliken **IoT Edge** i din IoT Hub.

1. Klicka på enhetens enhets-ID. Kopiera fältet **primär anslutnings sträng** .

1. Klistra in den i fältet enhets anslutnings sträng i Windows administrations Center. Välj sedan **etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har klistrat in enhetens anslutnings sträng](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. När etableringen är klar väljer du **Slutför**. Du kommer tillbaka till huvud instrument panelen. Nu bör du se en ny enhet som anges, vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge enheten för att ansluta till den. På sidan **Översikt** kan du visa **listan IoT Edge-modul** och **IoT Edge status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. I [Azure Portal](https://ms.portal.azure.com/)går du till fliken **IoT Edge** i din IoT Hub.

1. Klicka på enhetens enhets-ID. Kopiera fältet **primär anslutnings sträng** .

1. Klistra in över platshållartexten i följande kommando och kör den i en upphöjd PowerShell-session på mål enheten.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Alternativ 2: etablering via DPS med symmetriska nycklar

I det här avsnittet beskrivs hur du konfigurerar enheten automatiskt med hjälp av DPS och symmetriska nycklar.

# <a name="windows-admin-center"></a>[Windows administrations Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge enhets etablering** väljer du **symmetrisk nyckel (DPS)** från etablerings metoden i list rutan.

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till din DPS-instans.

1. Kopiera värdet för **ID-omfång** på fliken **Översikt** . Klistra in det i fältet scope-ID i administrations centret för Windows.

1. På fliken **Hantera registreringar** i Azure Portal väljer du den registrering som du har skapat. Kopiera värdet för **primär nyckel** i registrerings informationen. Klistra in den i fältet symmetrisk nyckel i Windows administrations Center.

1. Ange registrerings-ID för enheten i fältet registrerings-ID i administrations centret för Windows.

1. Välj **etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har fyllt i de obligatoriska fälten vid symmetrisk nyckel etablering](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. När etableringen är klar väljer du **Slutför**. Du kommer tillbaka till huvud instrument panelen. Nu bör du se en ny enhet som anges, vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge enheten för att ansluta till den. På sidan **Översikt** kan du visa **listan IoT Edge-modul** och **IoT Edge status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopiera följande kommando till en textredigerare. Ersätt platshållartexten med informationen som detaljerad.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till din DPS-instans.

1. Kopiera värdet för **ID-omfång** på fliken **Översikt** . Klistra in det via lämplig platshållartext i kommandot.

1. På fliken **Hantera registreringar** i Azure Portal väljer du den registrering som du har skapat. Kopiera värdet för **primär nyckel** i registrerings informationen. Klistra in det via lämplig platshållartext i kommandot.

1. Ange registrerings-ID för enheten för att ersätta lämplig platshållartext i kommandot.

1. Kör kommandot i en upphöjd PowerShell-session på mål enheten.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Alternativ 3: etablering via DPS med X. 509-certifikat

I det här avsnittet beskrivs hur du konfigurerar enheten automatiskt med hjälp av DPS-och X. 509-certifikat.

# <a name="windows-admin-center"></a>[Windows administrations Center](#tab/windowsadmincenter)

1. I fönstret **Azure IoT Edge enhets etablering** väljer du **X. 509-certifikat (DPS)** från etablerings metoden i list rutan.

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till din DPS-instans.

1. Kopiera värdet för **ID-omfång** på fliken **Översikt** . Klistra in det i fältet scope-ID i administrations centret för Windows.

1. Ange registrerings-ID för enheten i fältet registrerings-ID i administrations centret för Windows.

1. Ladda upp dina certifikat och privata nyckelfiler.

1. Välj **etablering med den valda metoden**.

   ![Välj etablering med den valda metoden när du har fyllt i de obligatoriska fälten för 509-certifikat etablering](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. När etableringen är klar väljer du **Slutför**. Du kommer tillbaka till huvud instrument panelen. Nu bör du se en ny enhet som anges, vars typ är `IoT Edge Devices` . Du kan välja den IoT Edge enheten för att ansluta till den. På sidan **Översikt** kan du visa **listan IoT Edge-modul** och **IoT Edge status** för enheten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopiera följande kommando till en textredigerare. Ersätt platshållartexten med informationen som detaljerad.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. I [Azure Portal](https://ms.portal.azure.com/)navigerar du till din DPS-instans.

1. Kopiera värdet för **ID-omfång** på fliken **Översikt** . Klistra in det via lämplig platshållartext i kommandot.

1. Ange registrerings-ID för enheten för att ersätta lämplig platshållartext i kommandot.

1. Ersätt lämplig platshållartext med den absoluta käll Sök vägen till certifikat filen.

1. Ersätt lämplig platshållartext med den absoluta käll Sök vägen till din privata nyckel fil.

1. Kör kommandot i en upphöjd PowerShell-session på mål enheten.

---

## <a name="verify-successful-configuration"></a>Verifiera lyckad konfiguration

Kontrol lera att IoT Edge för Linux på Windows har installerats och kon figurer ATS på IoT Edge enheten.

# <a name="windows-admin-center"></a>[Windows administrations Center](#tab/windowsadmincenter)

1. Välj din IoT Edge-enhet i listan över anslutna enheter i Windows administrations Center för att ansluta till den.

1. På sidan enhets översikt visas viss information om enheten:

    1. I avsnittet **IoT Edge modul List** visas körning av moduler på enheten. När IoT Edge tjänsten startar för första gången bör du bara se modulen **edgeAgent** som körs. EdgeAgent-modulen körs som standard och hjälper till att installera och starta ytterligare moduler som du distribuerar till din enhet.
    1. Avsnittet **IoT Edge status** visar tjänstens status och ska rapporteras som **aktiv (körs)**.

1. Om du behöver felsöka IoT Edge tjänsten använder du **kommando tolk** verktyget på enhets sidan för SSH (Secure Shell) i den virtuella datorn och kör Linux-kommandona.

    1. Hämta tjänstloggar om du behöver felsöka tjänsten.

       ```bash
       journalctl -u iotedge
       ```

    2. Använd `check` verktyget för att verifiera enhetens konfiguration och anslutnings status.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Logga in på IoT Edge för Linux på en virtuell Windows-dator med hjälp av följande kommando i PowerShell-sessionen:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Det enda konto som tillåts för SSH till den virtuella datorn är den användare som skapade den.

1. När du är inloggad kan du kontrol lera listan med att köra IoT Edge moduler med följande Linux-kommando:

   ```bash
   iotedge list
   ```

1. Om du behöver felsöka IoT Edge tjänsten använder du följande Linux-kommandon.

    1. Hämta tjänstloggar om du behöver felsöka tjänsten.

       ```bash
       journalctl -u iotedge
       ```

    2. Använd `check` verktyget för att verifiera enhetens konfiguration och anslutnings status.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Nästa steg

* Fortsätt att [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md) och lär dig hur du distribuerar moduler till din enhet.
* Lär dig hur du [hanterar certifikat på IoT Edge för Linux på en virtuell Windows-dator](how-to-manage-device-certificates.md) och överför filer från värd operativ systemet till den virtuella Linux-datorn.
* Lär dig hur du [konfigurerar IoT Edge enheter för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).
