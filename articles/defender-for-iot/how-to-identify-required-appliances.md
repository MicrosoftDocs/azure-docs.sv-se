---
title: Identifiera apparater som krävs
description: Läs mer om maskin vara och virtuella enheter för Certified Defender för IoT-sensorer och den lokala hanterings konsolen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2ad5bf08542cd98f7acae36827b1a7b284a893b0
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149313"
---
# <a name="identify-required-appliances"></a>Identifiera apparater som krävs

Den här artikeln innehåller information om Certified Defender for IoT sensor-enheter. Defender fort IoT kan distribueras på fysiska och virtuella enheter.

Detta inkluderar certifierade *förkonfigurerade* enheter, där program varan redan är installerad, samt icke-konfigurerade certifierade apparater där du kan hämta och installera nödvändig program vara.

Artikeln innehåller också specifikationer för en lokal hanterings konsol installation. Den lokala hanterings konsolen är inte tillgänglig som en förkonfigurerad installation.

- Om du vill köpa en förkonfigurerad sensor granskar du de modeller som är tillgängliga i avsnittet [sensor utrustning](#sensor-appliances) och fortsätter sedan med köpet.

- Om du vill köpa en egen installation granskar du de modeller som är tillgängliga i avsnittet [sensor utrustning](#sensor-appliances) och i avsnittet [ytterligare certifierade enheter](#additional-certified-appliances) . När du har skaffat enheten kan du ladda ned och installera program varan.

- Om du vill köpa den lokala hanterings konsolen läser du informationen i avsnittet [om den lokala hanterings konsolens utrustning](#on-premises-management-console-appliance) . När du har skaffat enheten kan du ladda ned och installera program varan.

När du har slutfört uppgifterna här kan du installera program varan och konfigurera nätverket.

## <a name="sensor-appliances"></a>Sensor utrustning

Defender for IoT stöder både fysiska och virtuella distributioner.

### <a name="physical-sensors"></a>Fysiska sensorer

Det här avsnittet innehåller en översikt över de fysiska sensor modeller som är tillgängliga. Du kan köpa sensorer med förkonfigurerade program eller köp sensorer som inte är förkonfigurerade.

| Distributions typ | Företag | Stora företag | Montering av SMB-rack| SMB har upprobusts|
|--|--|--|--|--|
| Bild | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Modell på företags nivå."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Modellen på företags nivå."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB-nivå modellen."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Den SMB-robusta nivå modellen."::: |
| Modell | HPE DL360 | HPE DL20 | HPE DL20 | HPE EL300 |
| Övervaka portar | Upp till 15 RJ45 eller 8 OPT | Upp till 8 RJ45 eller 6 OPT | 4 RJ45 | Upp till 5 |
| Maximal bandbredd [1](#anchortext) | 3 GB/SEK | 1 GB/SEK | 200 MB/SEK | 100 MB/SEK |
| Maximalt antal skyddade enheter | 30 000 | 15 000 | 1 000 | 800 |

Se [programspecifikationer](#appliance-specifications) för leverantörs information.

Om förkonfigurerade sensorer: Microsoft samarbetar med en pil för att tillhandahålla förinställda sensorer. Om du vill köpa en förkonfigurerad sensor kontaktar du pilen på följande adress: <hardware.sales@arrow.com>

Om att ta med din egen installation: granska de modeller som stöds som beskrivs här. När du har köpt din installation går du till **Defender för IoT**  >  **Network-sensorer ISO**-  >  **installation** för att ladda ned program varan.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Nätverks sensorer ISO.":::

<a id="anchortext">1</a> bandbredds kapacitet kan variera beroende på distribution av protokoll.

### <a name="virtual-sensors"></a>Virtuella sensorer

Det här avsnittet innehåller en översikt över de virtuella sensorer som är tillgängliga.

| Distributions typ | Företag | Stora företag | SMB |
|--|--|--|--|
| Maximal bandbredd | 2,5 GB/SEK | 800 MB/SEK | 160 MB/SEK |
| Maximalt antal skyddade enheter | 30 000 | 10 000 | 2 500 |

## <a name="on-premises-management-console-appliance"></a>Lokal hanterings konsol installation

Hanterings konsolen är tillgänglig som en virtuell distribution.

| Distributions typ | Stora företag |
|--|--|
| Typ av apparat | HPE DL20, VM |
| Antal hanterade sensorer | Upp till 300 |

När du har skaffat en lokal hanterings konsol går du till **Defender för**  >  **den lokala hanterings konsolen**  >  **ISO-installation** för att hämta ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Lokal hanterings konsol.":::

## <a name="appliance-specifications"></a>Specifikationer för utrustning

I det här avsnittet beskrivs specifikationer för maskin vara för följande utrustning:

- Företags distribution: HPE DL360

- Företags distribution: HPE DL20

- SMB-distribution: HPE DL20

- Virtual installation-specifikationer

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Företags distribution: HPE DL360

| Komponent | Tekniska specifikationer |
|--|--|
| Chassi | 1U rack Server |
| Dimensioner | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(i) |
| Vikt | Max 16,27 kg (35,86 lb) |
| Processor | Intel Xeon silver 4215 R 3,2 GHz, 11 M cache, 8c/16T, 130 W |
| 855 | Intel-C621 |
| Minne | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 6 x 1,2 – TB SAS 12G Enterprise 10K SFF (2,5 in) i Hot-Plug hård disk-RAID 5 |
| Nätverks styrenhet | Fordonsbaserad: 2 x 1 GB Broadcom BCM5720<br>LOM på kort: iDRAC port Card 1 – GB Broadcom BCM5720<br><br>Externt: 1 x Intel Ethernet i350 QP 1 – GB serveradapter, låg profil |
| Hantering | HPE iLO Advanced |
| Enhets åtkomst | Två baktill USB 3,0<br>En första USB 2,0<br>En intern USB 3,0 |
| Ström | 2 x HPE 500 med Flex-kortplats platina låg halogen Power Supply-paket |
| Rack support | HPE 1U Gen10 SFF Easy Install järnvägs kit |

### <a name="appliance-bom"></a>Apparat struktur

| PN | Beskrivning | Kvantitet |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | Europa – flerspråkig lokalisering | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO kit för DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R-paket för DL360 Gen10 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R Smart kit | 2 |
| 872479 – B21 | HPE 1,2 – TB SAS 10K SFF SC DS HDD | 6 |
| 811546 – B21 | HPE 1 – GbE 4-p BASE-T i350 adapter | 1 |
| P02377-B21 | HPE Smart hybrid Capacitor w \_ 145 mm-kabel | 1 |
| 804331 – B21 | HPE Smart Array P408i – en SR Gen10-styrenhet | 1 |
| 665240 – B21 | HPE 1 – GbE 4-p FLR-T i350 adapter | 1 |
| 871244 – B21 | HPE DL360 Gen10 High Performance fläkt kit | 1 |
| 865408 – B21 | HPE 500 – v FS-plats, hett plugg-paket för aktivering av LH | 2 |
| 512485 – B21 | Support för HPE iLO adv 1 – Server licens 1-år | 1 |
| 874543 – B21 | HPE 1U Gen10 SFF Easy Install järnvägs kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Företags distribution: HPE DL20

| Komponent | Tekniska specifikationer |
|--|--|
| Chassi | 1U rack Server |
| Mått (höjd x bredd x djup) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 tum |
| Vikt | 7,9 kg/17.41 lb |
| Processor | Intel Xeon E-2234, 3,6 GHz, land/8T, 71 W |
| 855 | Intel-C242 |
| Minne | 2 x 16 GB x8-DDR4 med dubbla rang-2666 |
| Storage | 3 x 1 – TB SATA 6G mitt linje 7,2 K SFF (2,5 in) – RAID 5 med Smart Array P408i – en SR-styrenhet |
| Nätverks styrenhet | Fordonsbaserad: 2 x 1 GB <br>Fordonsbaserad: iLO-Port kort 1 GB <br>Externt: 1 x HPE Ethernet 1 – GB 4-Port 366FLR-kort |
| Hantering | HPE iLO Advanced |
| Enhets åtkomst | Framtill: 1 x USB 3,0, 1 x USB iLO-tjänsteport <br>Bak sidan: 2 x USB 3,0 <br>Internt: 1 x USB 3,0 |
| Ström | Dual hot plug-nätaggregat 500 W |
| Rack support | HPE 1U kort friktions järnvägs paket |

### <a name="appliance-bom"></a>Apparat struktur

| PN | Beskrivning: hög slut | Kvantitet |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO-paket | 1 |
| 879507 – B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND kit | 2 |
| 655710 – B21 | HPE 1 – TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser kit | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4-Port 366FLR-kort | 1 |
| 782961 – B21 | HPE 12 – W Smart Storage-batteri | 1 |
| 869081 – B21 | HPE Smart Array P408i – en SR-G10 | 1 |
| 865408 – B21 | HPE 500 – v FS-plats, hett plugg-paket för aktivering av LH | 2 |
| 512485 – B21 | Support för HPE iLO adv 1 – Server licens 1-år | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS enableing FIO kit | 1 |
| 775612 – B21 | HPE 1U kort friktions järnvägs paket | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB-distribution: HPE DL20

| Komponent | Tekniska specifikationer |
|--|--|
| Chassi | 1U rack Server |
| Mått (höjd x bredd x djup) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 tum |
| Vikt | 7,88 kg/17.37 lb |
| Processor | Intel Xeon E-2224, 3,4 GHz, östra, 71 W |
| 855 | Intel-C242 |
| Minne | 1 x 8 GB med dubbla Rank x8-DDR4 – 2666 |
| Storage | 2 x 1 – TB SATA 6G mitt linje 7,2 K SFF (2,5 in) – RAID 1 med Smart Array P208i-a |
| Nätverks styrenhet | Fordonsbaserad: 2 x 1 GB <br>Fordonsbaserad: iLO-Port kort 1 GB <br>Externt: 1 x HPE Ethernet 1 – GB 4-Port 366FLR-kort |
| Hantering | HPE iLO Advanced |
| Enhets åtkomst | Framtill: 1 x USB 3,0, 1 x USB iLO-tjänsteport <br>Bak sidan: 2 x USB 3,0 <br>Internt: 1 x USB 3,0 |
| Ström | Hot plugg-strömförsörjning 290 W |
| Rack support | HPE 1U kort friktions järnvägs paket |

### <a name="appliance-bom"></a>Apparat struktur

| PN | Beskrivning | Kvantitet |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO-paket | 1 |
| 879505 – B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND-paket | 1 |
| 801882 – B21 | HPE 1 – TB SATA 7,2 K LFF RW-HÅRDDISK | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser kit | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4-Port 366FLR-kort | 1 |
| 869079 – B21 | HPE Smart Array E208i – en SR-G10 | 1 |
| P21649-B21 | HPE DL20 Gen10 plats 290 W FIO PSU kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC kabel kit | 1 |
| 512485 – B21 | Support för HPE iLO adv 1 – Server licens 1-år | 1 |
| 775612 – B21 | HPE 1U kort friktions järnvägs paket | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB robust: HPE Edgeline EL300

| Komponent | Tekniska specifikationer |
|--|--|
| Konstruktion | Aluminium, fanless & damm-korrektur design |
| Mått (höjd x bredd x djup) | 200.5 mm (7,9 ") hög, 232mm (9,14") brett av 100mm (3,9 ") djup |
| Vikt | 4,91 KG (10,83 kg) |
| Processor | Intel Core i7 – 8650U (1,9 GHz/4 kärnor/15W) |
| 855 | Hubb för Intel® Q170 Platform Controller |
| Minne | 8 GB DDR4 2133MHz wide temperatur SODIMM |
| Storage | 128 GB 3ME3 wide temperatur mSATA SSD |
| Nätverks styrenhet | 6x Gigabit Ethernet-portar med Intel® I219 |
| Enhets åtkomst  | 4 USBs: 2 framtill; 2 baktill; 1 intern |
| Nätadapter | 250V/10A |
| Ansluter | Monterings sats, din järnväg |
| Drift temperatur | 0C till + 70C  |
| Luftfuktighet | 10% ~ 90%, icke-kondenserande |
| Vibrationer | 0,3 Grms 10Hz till 300Hz, 15 minuter per axel – din järnväg   |
| Skydd | 10G 10ms, halva-sinus, tre för varje axel. (Både positiva & negativ puls) – din järnväg |

### <a name="appliance-bom"></a>Apparat struktur
| Produkt | Beskrivning |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 konvergerat Edge-system |
| P25828-B21 B19 | HPE EL300 v2 konvergerat Edge-system |
| P25833-B21 | Intel Core i7 – 8650U (1,9 GHz/4 kärnor/15W) FIO Basic processor Kit for HPE Edgeline EL300 |
| P09176-B21 | HPE Edgeline 8 GB (1x8GB) med dubbla rangordning x8 DDR4-2666 SODIMM-CAS-19-19-19-registrerat minne WT kit |
| P09188-B21 | HPE Edgeline 256GB SATA 6G Read intensivt M. 2 2242 3 miljoner Wty wide Temp SSD |
| P04054-B21 | HPE Edgeline EL300 SFF till M. 2 aktiverings paket |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO transfer Board |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC strömförsörjning |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1.83 m ström sladd |
| P25835-B21 | HPE EL300 v2 FIO Carrier Board |
| R1P49AAE | HPE EL300 iSM ADV 3 – dygnet runt Sup_Upd E-LTU |
| P08018 – B21 valfritt | HPE Edgeline EL300 Low Profile klammer kit  |
| P08019 – B21 valfritt | HPE Edgeline EL300 DIN järnvägs Mount-sats |
| P08020 – B21 valfritt | HPE Edgeline EL300 vägg Mount kit |
| P03456 – B21 valfritt | HPE Edgeline 1GbE 4-Port TSN FIO dotter kort |

## <a name="virtual-appliance-specifications"></a>Virtual installation-specifikationer

### <a name="sensors"></a>Sensorer

| Typ | Företag | Stora företag | SMB |
|--|--|--|--|
| Virtuell processor | 32 | 8 | 4 |
| Minne | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Lokal hanterings konsol installation

| Typ | Stora företag |
|--|--|
| Beskrivning | Virtuell installation för företags distributions typer |
| Virtuell processor | 8 |
| Minne | 32 GB |
| Storage | 1,8 TB |

Hypervisorer som stöds: VMware ESXi version 5,0 och senare, Hyper-V

## <a name="additional-certified-appliances"></a>Ytterligare certifierade apparater

I det här avsnittet beskrivs ytterligare enheter som certifierats av Microsoft men som inte erbjuds som förkonfigurerade apparater.

| Distributions typ | Stora företag |
|--|--|
| Bild | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Företags distributions typ."::: |
| Modell | Dell PowerEdge R340 XL |
| Övervaka portar | Upp till nio RJ45 eller sex OPT |
| Max bandbredd [1](#anchortext2)| 1 GB/SEK |
| Maximalt antal skyddade enheter | 10 000 |

<a id="anchortext2">En</a> Bandbredds kapaciteten kan variera beroende på protokoll distribution.

När du har köpt installationen går du till **Defender för IoT**  >  **Network-sensorer ISO**-  >  **installation** för att ladda ned program varan.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Nätverks sensorer ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Företags distribution: Dell PowerEdge R340 XL

| Komponent | Tekniska specifikationer |
|--|--|
| Chassi | 1U rack Server
| Dimensioner | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(i) |
| Vikt | Max 29,98 lb/13,6 kg |
| Processor | Intel Xeon E-2144G 3,6 GHz, 8 M cache, lands-8T, Turbo (71 W) |
| 855 | Intel-C246 |
| Minne | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 3 x 2 – TB 7,2 KB RPM SATA 6-Gbit/s 512n 3,5 – Hot-Plug hård disk-RAID 5 |
| Nätverks styrenhet | Fordonsbaserad: 2 x 1 GB Broadcom BCM5720<br>LOM på kort: iDRAC port Card 1 – GB Broadcom BCM5720 <br><br>Externt: 1 x Intel Ethernet i350 QP 1 – GB serveradapter, låg profil |
| Hantering | iDRAC 9 Enterprise |
| Enhets åtkomst | Två baktill USB 3,0 <br> En första USB 3,0 |
| Ström | Dual hot plug-nätaggregat 350 W |
| Rack support | ReadyRails II Glide-räler för verktygs lös montering i 4-post-rack med kvadratiska eller flertrådiga hål eller monterings montering i 4 – efter gängade hål-rack, med stöd för valfritt verktyg som är mindre kabel hanterings arm. |

## <a name="dell-r340-bom"></a>Dell R340-struktur

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340-struktur.":::

## <a name="next-steps"></a>Nästa steg

[Om Azure Defender för IoT-installation](how-to-install-software.md)

[Om installation av Azure Defender för IoT-nätverk](how-to-set-up-your-network.md)

