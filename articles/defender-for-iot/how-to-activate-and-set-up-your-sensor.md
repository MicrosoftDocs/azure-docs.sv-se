---
title: Aktivera och konfigurera sensor
description: Den här artikeln beskriver hur du loggar in och aktiverar en sensor konsol.
ms.date: 1/12/2021
ms.topic: how-to
ms.openlocfilehash: 2fdfa0dd7048bf39ae5b53a729aef578054b30ac
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779196"
---
# <a name="activate-and-set-up-your-sensor"></a>Aktivera och konfigurera sensor

I den här artikeln beskrivs hur du aktiverar en sensor och utför den första installationen.

Administratörs användare utför aktivering när de loggar in för första gången och när aktiverings hanteringen krävs. Installations programmet säkerställer att sensorn har kon figurer ATS för optimal identifiering och avisering.

Säkerhets analytiker och skrivskyddade användare kan inte aktivera en sensor eller generera ett nytt lösen ord.

## <a name="sign-in-and-activation-for-administrator-users"></a>Inloggning och aktivering för administratörs användare

Administratörer som loggar in för första gången bör kontrol lera att de har åtkomst till filer för aktivering och återställning av lösen ord som hämtades under sensor onboarding. Om inte, behöver de Azures säkerhets administratör, prenumerations deltagare eller prenumerations ägares behörigheter för att kunna generera dessa filer på Azure Defender för IoT-portalen.

### <a name="first-time-sign-in-and-activation-checklist"></a>Check lista för inloggning och aktivering av första gången

Innan du loggar in på sensor konsolen bör administratörs användare ha åtkomst till:

- Sensor-IP-adressen som definierades under installationen.

- Användar inloggnings uppgifter för sensorn. Om du har hämtat en ISO-enhet för sensorn använder du de standardautentiseringsuppgifter som du fick under installationen. Vi rekommenderar att du skapar en ny *Administratörs* användare efter aktiveringen.

- Ett initialt lösen ord. Om du har köpt en förkonfigurerad sensor från en pil måste du skapa ett lösen ord när du loggar in för första gången.

- Aktiverings filen som är associerad med den här sensorn. Filen genererades och hämtades under sensor onboarding på-portalen för Defender för IoT.

- Ett SSL/TLS-signerat certifikat som ditt företag behöver.

### <a name="about-activation-files"></a>Om aktiverings filer

Din sensor har publicerats till Azure Defender för IoT i ett särskilt hanterings läge:

| Typ av läge | Beskrivning |
|--|--|
| **Läge för moln anslutning** | Information som sensorn känner av visas i sensor konsolen. Aviserings information levereras också via IoT Hub och kan delas med andra Azure-tjänster, till exempel Azure Sentinel. |
| **Lokalt ansluten läge** | Information som sensorn känner av visas i sensor konsolen. Identifierings information delas också med den lokala hanterings konsolen, om sensorn är ansluten till den. |

En lokalt ansluten eller molnbaserad aktiverings fil genererades och hämtades för den här sensorn under onboarding. Aktiverings filen innehåller instruktioner för sensorns hanterings läge. *En unik aktiverings fil ska överföras till varje sensor som du distribuerar.*  Första gången du loggar in måste du ladda upp relevant aktiverings fil för den här sensorn.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender för IoT-portalen, onboard-sensor.":::

### <a name="about-certificates"></a>Om certifikat

Efter sensor installation skapas ett lokalt självsignerat certifikat som används för att få åtkomst till sensor konsolen. När en administratör loggar in till-konsolen för första gången uppmanas användaren att publicera ett SSL/TLS-certifikat.

Det finns två säkerhets nivåer:

- Svara på särskilda certifikat och krypterings krav som din organisation har begärt genom att ladda upp det CA-signerade certifikatet.
- Tillåt verifiering mellan hanterings konsolen och anslutna sensorer. Verifieringen utvärderas mot en lista över återkallade certifikat och certifikatets förfallo datum. *Om verifieringen Miss lyckas stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen.* Det här alternativet är aktiverat som standard efter installationen.  

Konsolen stöder följande certifikat typer:

- Privata och företags nyckel infrastruktur (privat PKI)

- Infrastruktur för offentliga nycklar (offentlig PKI)

- Lokalt genererat på produkten (lokalt självsignerat) 

  > [!IMPORTANT]
  > Vi rekommenderar att du inte använder det självsignerade standard certifikatet. Certifikatet är inte säkert och bör endast användas för test miljöer. Ägaren av certifikatet kan inte verifieras och säkerheten i systemet kan inte upprätthållas. Använd aldrig det här alternativet för produktions nätverk.

### <a name="sign-in-and-activate-the-sensor"></a>Logga in och aktivera sensorn

Logga in och aktivera:

1. Gå till sensor konsolen från din webbläsare genom att använda IP-adressen som definierats under installationen. Dialog rutan inloggning öppnas.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender för IoT-sensor.":::

1. Ange de autentiseringsuppgifter som definierats under sensor installationen eller Välj alternativet för **lösen ords återställning** . Om du har köpt en förkonfigurerad sensor från pilen måste du först skapa ett lösen ord. Mer information om lösen ords återställning finns i [Undersök lösen ords haveri vid inledande inloggning](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. När du har loggat in öppnas dialog rutan **Aktivera** . Välj **överför** och gå till den aktiverings fil som du laddade ned under sensor onboarding.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Välj överför och gå till aktiverings filen.":::

1. Välj länken **sensor nätverks konfiguration** om du vill ändra sensor nätverks konfigurationen före aktiveringen. Se [Uppdatera sensor nätverks konfiguration före aktivering](#update-sensor-network-configuration-before-activation).

1. Godkänn de allmänna villkoren.

1. Välj **Aktivera**. Dialog rutan SSL/TLS-certifikat öppnas.

1. Definiera ett certifikat namn.
1. Ladda upp CRT-och nyckelfil-filerna.
1. Ange en lösen fras och ladda upp en PEM-fil om det behövs.
1. Välj **Nästa**. Verifierings skärmen öppnas. Verifiering mellan hanterings konsolen och anslutna sensorer är aktive rad som standard.
1. Stäng av **Aktivera verifiering av systemomfattande verifiering** för att inaktivera verifiering. Vi rekommenderar att du aktiverar validering.
1. Välj **Spara**.  

Du kan behöva uppdatera skärmen när du har laddat upp det CA-signerade certifikatet.

Information om hur du laddar upp ett nytt certifikat, parametrar som stöds och arbetar med kommandona CLI-certifikat finns i [hantera enskilda sensorer](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Uppdatera sensor nätverks konfiguration före aktivering  

Parametrarna för nätverks konfiguration för sensorn definierades vid program varu installationen eller när du köpte en förkonfigurerad sensor. Följande parametrar har definierats:

- IP-adress
- DNS
- Standard gateway
- Nätmask
- Värdnamn

Du kanske vill uppdatera den här informationen innan du aktiverar sensorn. Du kan till exempel behöva ändra de förinställda parametrarna som definieras av pilen. Du kan också definiera proxyinställningar innan du aktiverar sensorn.

Uppdatera konfigurations parametrar för sensor nätverk:

1. Välj länken **sensor för nätverks konfiguration** formuläret i dialog rutan **Aktivera** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Sensor nätverks konfiguration.":::

2. De parametrar som definieras under installationen visas. Alternativet för att definiera proxyn är också tillgängligt. Uppdatera inställningarna efter behov och välj **Spara**.

### <a name="subsequent-sign-ins"></a>Efterföljande inloggningar

Efter första aktiveringen öppnas Azure Defender för IoT sensor-konsolen efter inloggning utan att en aktiverings fil krävs. Du behöver bara dina inloggnings uppgifter.

När du har loggat in öppnas Azure Defender för IoT-konsolen.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender för IoT-konsol.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Inledande installation och inlärning (för administratörer)

Efter din första inloggning börjar Azure Defender för IoT-sensorn att övervaka ditt nätverk automatiskt. Nätverks enheter visas i avsnitten enhets karta och enhets inventering. Azure Defender för IoT börjar identifiera och varna dig om alla säkerhets-och drifts incidenter som inträffar i nätverket. Du kan sedan skapa rapporter och frågor baserat på den identifierade informationen.

Inlednings vis utförs den här aktiviteten i inlärnings läge, vilket instruerar din sensor att lära sig din nätverks vanliga aktivitet. Sensorn lär sig till exempel enheter som identifierats i nätverket, protokoll som identifierats i nätverket och fil överföringar mellan vissa enheter. Den här aktiviteten blir nätverkets bas linje aktivitet.

### <a name="review-and-update-basic-system-settings"></a>Granska och uppdatera grundläggande Systeminställningar

Granska sensorns Systeminställningar för att se till att sensorn är konfigurerad för optimal identifiering och avisering.

Definiera sensorns system inställningar. Exempel:

- Definiera ICS (eller IoT) och åtskiljda undernät.

- Definiera port Ali Aset för sitespecifika protokoll.

- Definiera VLAN och namn som används.

- Om DHCP används definierar du giltiga DHCP-intervall.

- Definiera integration med Active Directory och e-postserver efter behov.

### <a name="disable-learning-mode"></a>Inaktivera inlärnings läge

När du har justerat systeminställningarna kan du låta Azure Defender for IoT-sensorn köras i inlärnings läge tills du känner till att system identifieringen återspeglar din nätverks aktivitet korrekt.

Inlärnings läget bör köras i ungefär 2 till 6 veckor, beroende på din nätverks storlek och komplexitet. När du har inaktiverat inlärnings läget utlöser en aktivitet som skiljer sig från din bas linje aktivitet en avisering.

Så här inaktiverar du inlärnings läget:

- Välj **Systeminställningar** och inaktivera **inlärnings** alternativet.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Inloggning första gången för säkerhetsanalytiker och skrivskyddade användare

Innan du loggar in kontrollerar du att du har:

- Sensorns IP-adress.
- Inloggnings uppgifter som administratören har angett.

## <a name="console-tools-overview"></a>Konsol verktyg: översikt

Du kommer åt konsol verktyg från sido menyn.

**Navigering** 

| Fönster | Ikon | Beskrivning |
| -----------|--|--|
| Instrumentpanel | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Visa en intuitiv ögonblicks bild av status för nätverkets säkerhet. |
| Enhets karta | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Visa nätverks enheter, enhets anslutningar och enhets egenskaper på en karta. Olika zoomnings-, markerings-och filter alternativ är tillgängliga för visning av ditt nätverk. |
| Enhets inventering | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | Enhets inventeringen visar en lista över enhets egenskaper som sensorn känner av. Alternativen är tillgängliga för att: <br /> – Sortera eller filtrera informationen enligt tabell fälten och se den filtrerade informationen som visas. <br /> – Exportera information till en CSV-fil. <br /> – Importera information om Windows-registret.|
| Aviseringar | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Visa aviseringar när princip överträdelser inträffar, avvikelser från bas linje beteendet inträffar eller någon typ av misstänkt aktivitet i nätverket identifieras. |
| Rapporter | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Visa rapporter som baseras på data utvinnings frågor. |

**Analys**

| Fönster| Ikon | Beskrivning |
|---|---|---|
| Händelse tids linje | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Visa en tids linje med information om aviseringar, nätverks händelser (information) och användar åtgärder, till exempel användar inloggningar och användar borttagningar.|

**Navigering**

| Fönster | Ikon | Beskrivning |
|---|---|---|
| Data utvinning | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Generera omfattande och detaljerad information om dina nätverks enheter på olika lager. |
| Undersökning | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Visa trender och statistik i en mängd olika widgetar. |
| Riskbedömning | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Visa fönstret **sårbarheter** . |

**Administratör**

| Fönster | Ikon | Beskrivning |
|---|---|---|
| Användare | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Definiera användare och roller med olika åtkomst nivåer. |
| Styr | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Vidarebefordra aviserings information till partner och interna källor (till exempel Azure Sentinel) som integreras med Defender för IoT, till e-postadresser, till webhook-servrar med mera. <br /> Mer information finns i [vidarebefordra aviserings information](how-to-forward-alert-information-to-partners.md) . |
| Systeminställningar | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Konfigurera systeminställningarna. Definiera till exempel DHCP-inställningar, ange information om e-postservern eller skapa port Ali Aset. |
| Importera inställningar | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Visa fönstret **Importera inställningar** . Du kan utföra manuella ändringar i en enhets information.<br /> Mer information finns i [Importera enhets information](how-to-import-device-information.md) . |

**Support**

| Fönster| Ikon | Beskrivning |
|----|---|---|
| Support | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Kontakta [Microsoft Support](https://support.microsoft.com/) om du behöver hjälp. |

## <a name="see-also"></a>Se även

[Publicera en sensor](getting-started.md#onboard-a-sensor)

[Hantera sensor aktiverings filer](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Styr vilken trafik som övervakas](how-to-control-what-traffic-is-monitored.md)
