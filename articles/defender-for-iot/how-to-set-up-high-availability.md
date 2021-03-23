---
title: Konfigurera hög tillgänglighet
description: Öka återhämtnings förmågan hos din Defender för IoT-distribution genom att installera en lokal hanterings konsol med hög tillgänglighet. Distributioner med hög tillgänglighet säkerställer att dina hanterade sensorer kontinuerligt rapporterar till en aktiv lokal hanterings konsol.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781661"
---
# <a name="about-high-availability"></a>Om hög tillgänglighet

Öka återhämtnings förmågan hos din Defender för IoT-distribution genom att installera en lokal hanterings konsol med hög tillgänglighet. Distributioner med hög tillgänglighet säkerställer att dina hanterade sensorer kontinuerligt rapporterar till en aktiv lokal hanterings konsol.

Den här distributionen implementeras med ett lokalt hanterings konsol par som innehåller en primär och sekundär apparat.

## <a name="about-primary-and-secondary-communication"></a>Om primär och sekundär kommunikation

När en primär och sekundär hanterings konsol är kopplad till varandra:

- Ett SSL-certifikat för en lokal hanterings konsol används för att skapa en säker anslutning mellan de primära och sekundära enheterna. SSL kan vara det självsignerade certifikatet som installeras som standard eller ett certifikat som installeras av kunden.

- Den primära lokala hanterings konsol data säkerhets kopie ras automatiskt till den sekundära lokala hanterings konsolen var 10: e minut. Konfigurationerna och enhets data för den lokala hanterings konsolen säkerhets kopie ras. PCAP-filer och loggar ingår inte i säkerhets kopian. Du kan säkerhetskopiera och återställa PCAPs och loggarna manuellt.

- Den primära installationen i hanterings konsolen är dubblerad på den sekundära. till exempel system inställningar. Om inställningarna uppdateras på den primära servern, uppdateras de också på den sekundära.

- Innan licensen för den sekundära licensen går ut bör du definiera den som primär för att kunna uppdatera licensen.

## <a name="about-failover-and-failback"></a>Om redundans och återställning efter fel

Om en sensor inte kan ansluta till den primära lokala hanterings konsolen ansluts den automatiskt till den sekundära. Ditt system kommer att stödjas av både den primära och sekundära samtidigt, om mindre än hälften av sensorerna kommunicerar med den sekundära. Den sekundära tar över när mer än hälften av sensorer kommunicerar med den. Redundans från den primära till den sekundära tar cirka tre minuter. När redundansväxlingen inträffar låser den primära lokala hanterings konsolen. När detta inträffar kan du logga in på den sekundära med samma inloggnings uppgifter.

Vid redundansväxling fortsätter sensorer att försöka kommunicera med den primära enheten. Om fler än hälften av de hanterade sensorer lyckas kommunicera med den primära, återställs den primära. Följande meddelande visas i den sekundära konsolen när den primära återställs.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Skärm bild av ett meddelande som visas på den sekundära konsolen när den primära återställs.":::

Logga in igen på den primära installationen efter omdirigeringen.

## <a name="high-availability-setup-overview"></a>Översikt över inställningar för hög tillgänglighet

Installations-och konfigurations procedurerna utförs i fyra huvud steg:

1. Installera en primär installation av en lokal hanterings konsol. 

2. Konfigurera den lokala hanterings konsolens primära installation. Till exempel inställningar för schemalagd säkerhets kopiering, VLAN-inställningar. Mer information finns i användar handboken för lokal hanterings konsol. Alla inställningar tillämpas på den sekundära installationen automatiskt efter länkning.

3. Installera en sekundär installation av en lokal hanterings konsol. Mer information finns i [om Defender for IoT-installation](how-to-install-software.md).

4. Para ihop de primära och sekundära hanterings konsolens anläggningar enligt beskrivningen [här](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). Den primära lokala hanterings konsolen måste hantera minst två sensorer för att kunna utföra installationen.

## <a name="high-availability-requirements"></a>Krav för hög tillgänglighet

Kontrol lera att du har uppfyllt följande krav för hög tillgänglighet:

- Certifikatkrav

- Program-och maskin varu krav

- Krav för nätverks åtkomst

### <a name="software-and-hardware-requirements"></a>Program-och maskin varu krav

- Både den primära och sekundära lokala hanterings konsolens utrustning måste köra identiska maskin varu modeller och program varu versioner.

- Systemet med hög tillgänglighet kan bara konfigureras av Defender för IoT-användare med hjälp av CLI-verktyg.

### <a name="network-access-requirements"></a>Krav för nätverks åtkomst

Du måste kontrol lera att din organisations säkerhets princip ger dig åtkomst till följande tjänster på den primära och sekundära lokala hanterings konsolen. Dessa tjänster tillåter också anslutningen mellan sensorer och den sekundära lokala hanterings konsolen:

|Port|Tjänst|Beskrivning|
|----|-------|-----------|
|**443 eller TCP**|HTTPS|Ger åtkomst till webb konsolen för den lokala hanterings konsolen.|
|**22 eller TCP**|SSH|Synkroniserar data mellan den primära och sekundära lokala hanterings konsolens enheter|
|**123 eller UDP**|NTP| Den lokala hanterings konsolens NTP-tidssynkronisering. Kontrol lera att de aktiva och passiva enheterna definieras med samma tidszon.|

## <a name="create-the-primary-and-secondary-pair"></a>Skapa det primära och sekundära paret

Kontrol lera att både den primära och sekundära lokala hanterings konsolens enheter är påslagna innan du startar proceduren.  

### <a name="on-the-primary"></a>På den primära

1. Logga in på CLI som en Defender för IoT-användare.

2. Kör följande kommando på den primära:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>I det här dokumentet kallas den huvudsakliga lokala hanterings konsolen som primär, och agenten kallas sekundär.

3. Ange IP-adressen för den sekundära enheten i ```<Secondary ip>``` fältet och välj RETUR. IP-adressen verifieras sedan och SSL-certifikatet laddas ned till den primära. Genom att ange IP-adressen associeras även sensorer till den sekundära enheten.

4. Kör följande kommando på den primära för att kontrol lera att certifikatet är korrekt installerat:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Kör följande kommando på den primära. **Kör inte med sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Detta möjliggör anslutningen mellan de primära och sekundära enheterna för säkerhets kopiering och återställning mellan dem.

6. Ange IP-adressen för den sekundära och välj RETUR.

### <a name="on-the-secondary"></a>På den sekundära

1. Logga in på CLI som en Defender för IoT-användare.

2. Kör följande kommando på den sekundära. **Kör inte med sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Detta tillåter anslutningen mellan de primära och sekundära enheterna för säkerhets kopiering och återställning mellan dem.

3. Ange den primära IP-adressen och tryck på RETUR.

### <a name="track-high-availability-activity"></a>Spåra aktivitet med hög tillgänglighet

De centrala program loggarna kan exporteras till support teamet för Defender for IoT för att hantera eventuella problem med hög tillgänglighet.  

För att komma åt kärn loggarna:

1. Välj **Exportera** från fönstret **system inställningar** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Uppdatera den lokala hanterings konsolen med hög tillgänglighet

Utför uppdateringen med hög tillgänglighet i följande ordning. Se till att varje steg är klart innan du påbörjar ett nytt steg.

Så här uppdaterar du med hög tillgänglighet:

1. Uppdatera den primära lokala hanterings konsolen.

2. Uppdatera den sekundära lokala hanterings konsolen.

3. Uppdatera sensorerna.

## <a name="see-also"></a>Se även

[Aktivera och konfigurera den lokala hanteringskonsolen](how-to-activate-and-set-up-your-on-premises-management-console.md)