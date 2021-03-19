---
title: Installera den lokala datagatewayen
description: Innan du kan komma åt data lokalt från Azure Logic Apps kan du hämta och installera den lokala datagatewayen
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 4b2559ad20036870c6df5c0662bb973f35155bfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576806"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installera lokal datagateway för Azure Logic Apps

Innan du kan [ansluta till lokala data källor från Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md)kan du hämta och installera den lokala [datagatewayen](https://aka.ms/on-premises-data-gateway-installer) på en lokal dator. Gatewayen fungerar som en brygga som ger snabb dataöverföring och kryptering mellan lokala datakällor och dina logikappar. Du kan använda samma Gateway-installation med andra moln tjänster, t. ex. energi automatisering, Power BI, Power Apps och Azure Analysis Services. Information om hur du använder gatewayen med dessa tjänster finns i följande artiklar:

* [Microsoft Power automatisering av lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

Den här artikeln visar hur du hämtar, installerar och konfigurerar din lokala datagateway så att du kan komma åt lokala data källor från Azure Logic Apps. Du kan också lära dig mer om [hur data gatewayen fungerar](#gateway-cloud-service) senare i det här avsnittet. Mer information om gatewayen finns i [Vad är en lokal gateway](/data-integration/gateway/service-gateway-onprem)? Om du vill automatisera Gateway-installation och hanterings uppgifter går du till PowerShell-galleriet för [DataGateway PowerShell-cmdletar](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har ett Azure-konto med en prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Ditt Azure-konto måste vara antingen ett arbets konto eller ett skol konto, som ser ut som `username@contoso.com` . Du kan inte använda Azure B2B-konton (gäst) eller personliga Microsoft-konton, till exempel @hotmail.com eller @outlook.com .

    > [!NOTE]
    > Om du har registrerat dig för ett Microsoft 365 erbjudande och inte angav din e-postadress för arbetet kan din adress se ut `username@domain.onmicrosoft.com` . Ditt konto lagras i en Azure AD-klient. I de flesta fall är användarens huvud namn (UPN) för ditt Azure-konto detsamma som din e-postadress.

    Om du vill använda en [Visual Studio Standard-prenumeration](https://visualstudio.microsoft.com/vs/pricing/) som är associerad med en Microsoft-konto måste du först [skapa en Azure AD-klient](../active-directory/develop/quickstart-create-new-tenant.md) eller använda standard katalogen. Lägg till en användare med ett lösen ord till katalogen och ge sedan användaren åtkomst till din Azure-prenumeration. Sedan kan du logga in under Gateway-installationen med det här användar namnet och lösen ordet.

  * Ditt Azure-konto måste tillhöra en enda [Azure Active Directory (Azure AD)-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology). Du måste använda samma Azure-konto för att installera och administrera gatewayen på den lokala datorn.

  * När du installerar gatewayen loggar du in med ditt Azure-konto, som länkar din gateway-installation till ditt Azure-konto och bara det kontot. Du kan inte länka samma Gateway-installation över flera Azure-konton eller Azure AD-klienter.

  * Senare i Azure Portal måste du använda samma Azure-konto för att skapa en Azure gateway-resurs som länkar till din gateway-installation. Du kan bara länka en gateway-installation och en Azure gateway-resurs till varandra. Ditt Azure-konto kan dock länka till olika Gateway-installationer som är associerade med en Azure gateway-resurs. Dina Logi Kap par kan sedan använda den här Gateway-resursen i utlösare och åtgärder som kan komma åt lokala data källor.

* Här följer krav för den lokala datorn:

  **Minimikrav**

  * .NET Framework 4.7.2
  * 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

  **Rekommenderade krav**

  * 8 kärnor-CPU
  * 8 GB minne
  * 64-bitars version av Windows Server 2012 R2 eller senare
  * SSD-lagring (Solid-State Drive) för buffring

  > [!NOTE]
  > Gatewayen stöder inte Windows Server Core.

* **Relaterade överväganden**

  * Installera endast den lokala datagatewayen på en lokal dator, inte en domänkontrollant. Du behöver inte installera gatewayen på samma dator som data källan. Du behöver bara en gateway för alla dina data källor, så du behöver inte installera gatewayen för varje data källa.

    > [!TIP]
    > För att minimera svars tiden kan du installera gatewayen så nära som möjligt för din data källa, eller på samma dator, förutsatt att du har behörighet.

  * Installera gatewayen på en lokal dator som är ansluten till Internet, som är ansluten till Internet, alltid aktive rad och inte går i ström spar läge. I annat fall kan gatewayen inte köras och prestanda kan bli lidande över ett trådlöst nätverk.

  * Om du planerar att använda Windows-autentisering kontrollerar du att du installerar gatewayen på en dator som är medlem i samma Active Directory-miljö som dina data källor.

  * Den region som du väljer för din gateway-installation är samma plats som du måste välja när du senare skapar Azure Gateway-resursen för din Logic app. Som standard är den här regionen samma plats som din Azure AD-klient som hanterar ditt Azure-användarkonto. Du kan dock ändra platsen under installationen av gatewayen eller senare.

    > [!IMPORTANT]
    > Under Gateway-installationen är kommandot **ändra region** inte tillgängligt om du har loggat in med ditt Azure Government-konto, som är associerat med en Azure Active Directory-klient (Azure AD) i [Azure Government molnet](../azure-government/compare-azure-government-global-azure.md). Gatewayen använder automatiskt samma region som användar kontots Azure AD-klient.
    > 
    > Om du vill fortsätta använda ditt Azure Government-konto, men konfigurera gatewayen att fungera i det globala Azure-molnet för flera innehavare i stället, måste du först logga in under Gateway-installationen med `prod@microsoft.com` användar namnet. Den här lösningen tvingar gatewayen att använda det globala Azure-molnet för flera innehavare, men du kan fortfarande fortsätta att använda ditt Azure Government-konto.

  * Om du uppdaterar din gateway-installation måste du först avinstallera din aktuella Gateway för att få en renare upplevelse.

    Vi rekommenderar att du kontrollerar att du använder en version som stöds. Microsoft släpper en ny uppdatering av den lokala datagatewayen varje månad och stöder för närvarande bara de senaste sex versionerna för den lokala datagatewayen. Om du får problem med den version som du använder kan du försöka att [Uppgradera till den senaste versionen](https://aka.ms/on-premises-data-gateway-installer) eftersom problemet kan lösas i den senaste versionen.

  * Gatewayen har två lägen: standard läge och personligt läge, som endast gäller för Power BI. Du kan inte ha mer än en gateway som körs i samma läge på samma dator.

  * Azure Logic Apps stöder Läs-och skriv åtgärder via gatewayen. Dessa åtgärder har dock [gränser för deras nytto Last storlek](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installera datagateway

1. [Hämta och kör installations programmet för gatewayen på en lokal dator](https://aka.ms/on-premises-data-gateway-installer).

1. Granska minimi kraven, behåll standard installations Sök vägen, Godkänn användnings villkoren och välj sedan **Installera**.

   ![Granska krav och godkänn användnings villkoren](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. När gatewayen har installerats, ange e-postadressen för ditt Azure-konto och välj sedan **Logga** in, till exempel:

   ![Logga in med arbets-eller skol konto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Gateway-installationen kan bara länka till ett Azure-konto.

1. Välj **Registrera en ny gateway på den här datorn**  >  **Nästa**. Det här steget registrerar din gateway-installation med [moln tjänsten Gateway](#gateway-cloud-service).

   ![Registrera gateway på den lokala datorn](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Ange den här informationen för din gateway-installation:

   * Ett Gateway-namn som är unikt för Azure AD-klienten
   * Återställnings nyckeln, som måste innehålla minst åtta tecken, som du vill använda
   * Bekräftelse för återställnings nyckeln

   ![Ange information för gateway-installation](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Spara och behåll återställnings nyckeln på en säker plats. Du behöver den här nyckeln om du vill ändra plats, flytta, återställa eller ta över en gateway-installation.

   Observera alternativet att **lägga till i ett befintligt Gateway-kluster**, som du väljer när du installerar ytterligare gatewayer för [scenarier med hög tillgänglighet](#high-availability).

1. Kontrol lera regionen för gateway-moln tjänsten och [Azure Service Bus meddelande instansen](../service-bus-messaging/service-bus-messaging-overview.md) som används av din gateway-installation. Som standard är den här regionen samma plats som Azure AD-klienten för ditt Azure-konto.

   ![Bekräfta region för Gateway-tjänsten och Service Bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Om du vill acceptera standard regionen väljer du **Konfigurera**. Men om standard regionen inte är den som är närmast dig kan du ändra regionen.

   *Varför ska du ändra region för din gateway-installation?*

   Om du till exempel vill minska svars tiden kan du ändra din gateways region till samma region som din Logic app. Eller så kan du välja den region som är närmast din lokala data källa. Din *gateway-resurs i Azure* och din Logic-app kan ha olika platser.

   1. Bredvid den aktuella regionen väljer du **ändra region**.

      ![Ändra aktuell gateway-region](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Öppna listan **Välj region** på nästa sida, Välj den region som du vill använda och välj sedan **Slutför**.

      ![Välj en annan region för Gateway-tjänsten](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Granska informationen i det slutliga bekräftelse fönstret. I det här exemplet används samma konto för Logic Apps, Power BI, Power Apps och Power automatisering, så gatewayen är tillgänglig för alla dessa tjänster. När du är klar väljer du **Stäng**.

   ![Bekräfta data Gateway-information](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Skapa nu Azure-resursen för din gateway-installation](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>Kontrol lera eller justera kommunikations inställningar

Den lokala datagatewayen är beroende av [Azure Service Bus meddelanden](../service-bus-messaging/service-bus-messaging-overview.md) för moln anslutningar och upprättar motsvarande utgående anslutningar till gatewayens associerade Azure-region. Om din arbets miljö kräver att trafiken går via en proxy eller brand vägg för att få åtkomst till Internet, kan denna begränsning förhindra att den lokala datagatewayen ansluter till moln tjänsten för gateway och Azure Service Bus meddelande hantering. Gatewayen har flera kommunikations inställningar, som du kan justera.

Ett exempel scenario är hur du använder anpassade anslutningar som ansluter till lokala resurser med hjälp av den lokala datagateway-resursen i Azure. Om du också har en brand vägg som begränsar trafik till vissa IP-adresser, måste du konfigurera gateway-installationen så att den tillåter åtkomst för motsvarande *hanterade anslutningar [utgående IP-adresser](logic-apps-limits-and-config.md#outbound)*. *Alla* Logic Apps i samma region använder samma IP-adressintervall.

Mer information finns i de här ämnena:

* [Justera kommunikationsinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-communication)
* [Konfigurera proxyinställningar för den lokala datagatewayen](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Stöd för hög tillgänglighet

För att undvika enskilda fel punkter för lokal data åtkomst kan du ha flera gateway-installationer (endast standard läge) med var och en på en annan dator och ange dem som ett kluster eller en grupp. På så sätt kan data begär Anden dirigeras till den andra gatewayen, och så vidare, om den primära gatewayen inte är tillgänglig. Eftersom du bara kan installera en standard-gateway på en dator måste du installera varje ytterligare gateway i klustret på en annan dator. Alla anslutningar som fungerar med den lokala datagatewayen stöder hög tillgänglighet.

* Du måste redan ha minst en gateway-installation med samma Azure-konto som den primära gatewayen och återställnings nyckeln för den installationen.

* Din primära Gateway måste köra Gateway-uppdateringen från november 2017 eller senare.

När du har konfigurerat din primära Gateway, väljer du **Lägg till i ett befintligt Gateway-kluster** när du går för att installera en annan gateway, väljer den primära gatewayen, som är den första gatewayen som du har installerat och anger återställnings nyckeln för den gatewayen. Mer information finns i [kluster med hög tillgänglighet för lokal datagateway](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändra plats, migrera, återställa eller ta över en befintlig gateway

Om du måste ändra gatewayens plats, flytta Gateway-installationen till en ny dator, återställa en skadad gateway eller bli ägare till en befintlig gateway, behöver du återställnings nyckeln som angavs under Gateway-installationen.

> [!NOTE]
> Innan du återställer gatewayen på den dator som har den ursprungliga Gateway-installationen måste du först avinstallera gatewayen på den datorn. Den här åtgärden kopplar från den ursprungliga gatewayen.
> Om du tar bort eller tar bort ett Gateway-kluster för en moln tjänst kan du inte återställa klustret.

1. Kör installations programmet för gateway på den dator som har den befintliga gatewayen.

1. När installations programmet har öppnats loggar du in med samma Azure-konto som användes för att installera gatewayen.

1. Välj **migrera, Återställ eller ta över en befintlig gateway**  >  **härnäst**, till exempel:

   ![Välj "migrera, Återställ eller överköps en befintlig gateway"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Välj bland tillgängliga kluster och gatewayer och ange återställnings nyckeln för den valda gatewayen, till exempel:

   ![Välj Gateway och ange återställnings nyckel](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Om du vill ändra region väljer du **ändra region** och sedan den nya regionen.

1. När du är klar väljer du **Konfigurera** så att du kan slutföra uppgiften.

## <a name="tenant-level-administration"></a>Administration på klient nivå

För att få insyn i alla lokala datagatewayer i en Azure AD-klient kan globala administratörer i den klienten logga in på [Power Platform administrations Center](https://powerplatform.microsoft.com) som innehavaradministratör och välja alternativet **datagatewayer** . Mer information finns i [Administration på klient nivå för den lokala datagatewayen](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Starta om gateway

Som standard körs Gateway-installationen på den lokala datorn som ett Windows-tjänst konto med namnet "lokal datagateway-tjänst". Gateway-installationen använder dock `NT SERVICE\PBIEgwService` namnet på "logga in som"-autentiseringsuppgifter och har behörighet att logga in som en tjänst.

> [!NOTE]
> Ditt Windows-tjänstkonto skiljer sig från det konto som används för att ansluta till lokala data källor och från det Azure-konto som du använder när du loggar in på Cloud Services.

Precis som med andra Windows-tjänster kan du starta och stoppa gatewayen på olika sätt. Mer information finns i [starta om en lokal datagateway](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Så här fungerar gatewayen

Användare i din organisation kan komma åt lokala data som de redan har behörighet till. Innan dessa användare kan ansluta till din lokala data källa måste du dock installera och konfigurera en lokal datagateway. Vanligt vis är en administratör den person som installerar och konfigurerar en gateway. De här åtgärderna kan kräva Server administratörs behörighet eller särskild kunskap om dina lokala servrar.

Gatewayen hjälper till att under lätta snabbare och säkrare bakom kommunikationen i bakgrunden. Den här kommunikationen flödar mellan en användare i molnet, moln tjänsten gateway och den lokala data källan. Gateway-moln tjänsten krypterar och lagrar autentiseringsuppgifter för data källan och gateway-informationen. Tjänsten dirigerar även frågor och resultat mellan användaren, gatewayen och din lokala data källa.

Gatewayen fungerar med brand väggar och använder endast utgående anslutningar. All trafik kommer som säker utgående trafik från Gateway-agenten. Gatewayen skickar data från lokala källor på krypterade kanaler via [Azure Service Bus meddelande hantering](../service-bus-messaging/service-bus-messaging-overview.md). Den här Service Bus skapar en kanal mellan gatewayen och den anropande tjänsten, men lagrar inte några data. Alla data som passerar genom gatewayen krypteras.

![Arkitektur för lokal datagateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Beroende på moln tjänsten kan du behöva konfigurera en data källa för gatewayen.

Dessa steg beskriver vad som händer när du interagerar med ett element som är anslutet till en lokal data Källa:

1. Moln tjänsten skapar en fråga, tillsammans med de krypterade autentiseringsuppgifterna för data källan. Tjänsten skickar sedan frågan och autentiseringsuppgifterna till gateway-kön för bearbetning.

1. Gateway-moln tjänsten analyserar frågan och skickar begäran till Azure Service Bus meddelanden.

1. Azure Service Bus Messaging skickar väntande begär anden till gatewayen.

1. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till en eller flera datakällor med dessa autentiseringsuppgifter.

1. Gatewayen skickar frågan till data källan för körning.

1. Resultaten skickas från data källan tillbaka till gatewayen och sedan till moln tjänsten Gateway. Gateway-moln tjänsten använder sedan resultaten.

### <a name="authentication-to-on-premises-data-sources"></a>Autentisering till lokala datakällor

En lagrad autentiseringsuppgift används för att ansluta från gatewayen till lokala data källor. Oavsett användare använder gatewayen lagrade autentiseringsuppgifter för att ansluta. Det kan finnas autentiserings undantag för vissa tjänster, till exempel DirectQuery och LiveConnect för Analysis Services i Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsofts moln tjänster använder [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) för att autentisera användare. En Azure AD-klient innehåller användar namn och säkerhets grupper. Normalt är e-postadressen som du använder för inloggning detsamma som användarens huvud namn (UPN) för ditt konto.

### <a name="what-is-my-upn"></a>Vad är mitt UPN?

Om du inte är en domän administratör kanske du inte känner till ditt UPN. Om du vill hitta UPN för ditt konto kör du `whoami /upn` kommandot från din arbets Station. Även om resultatet ser ut som en e-postadress, är resultatet UPN för ditt lokala domän konto.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synkronisera ett lokalt Active Directory med Azure AD

UPN för lokala Active Directory-konton och Azure AD-konton måste vara samma. Se därför till att varje lokalt Active Directory konto matchar ditt Azure AD-konto. Moln tjänsterna vet bara om konton i Azure AD. Så du behöver inte lägga till ett konto i din lokala Active Directory. Om kontot inte finns i Azure AD kan du inte använda det kontot.

Här är några sätt som du kan matcha dina lokala Active Directory-konton med Azure AD.

* Lägg till konton manuellt i Azure AD.

  Skapa ett konto i Azure Portal eller i Microsoft 365 administrations Center. Kontrol lera att konto namnet matchar UPN för det lokala Active Directory kontot.

* Synkronisera lokala konton med Azure AD-klienten med hjälp av Azure Active Directory Connect-verktyget.

  Azure AD Connect-verktyget innehåller alternativ för Directory-synkronisering och konfiguration av autentisering. De här alternativen omfattar hash-synkronisering av lösen ord, direktautentisering och Federation. Om du inte är innehavaradministratör eller en lokal domän administratör kan du kontakta IT-administratören för att få Azure AD Connect konfigurerat. Azure AD Connect garanterar att ditt Azure AD UPN matchar ditt lokala Active Directory UPN. Den här matchningen hjälper om du använder Analysis Services Live-anslutningar med Power BI eller funktioner för enkel inloggning (SSO).

  > [!NOTE]
  > Om du synkroniserar konton med Azure AD Connect-verktyget skapas nya konton i Azure AD-klienten.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Vanliga frågor och felsökning

* [Vanliga frågor och svar om lokal datagateway](/data-integration/gateway/service-gateway-onprem-faq)
* [Felsöka den lokala datagatewayen](/data-integration/gateway/service-gateway-tshoot)
* [Övervaka och optimera gatewayprestanda](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala data från Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Enterprise-integrerings funktioner](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Anslutningsprogram för Azure Logic Apps](../connectors/apis-list.md)
