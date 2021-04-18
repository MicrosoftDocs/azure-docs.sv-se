---
title: Skala HPC-program – Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du skalar HPC-program på virtuella Azure-datorer.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600972"
---
# <a name="scaling-hpc-applications"></a>Skala HPC-program

Optimal uppskalning och utskalning av HPC-program i Azure kräver experiment för prestandajustering och optimering för den specifika arbetsbelastningen. Det här avsnittet och de vm-seriespecifika sidorna ger allmän vägledning för att skala dina program.

## <a name="application-setup"></a>Programkonfiguration
[Azurehpc-lagringsplatsen](https://github.com/Azure/azurehpc) innehåller många exempel på:
- Konfigurera och köra [program](https://github.com/Azure/azurehpc/tree/master/apps) optimalt.
- Konfiguration [av filsystem och kluster](https://github.com/Azure/azurehpc/tree/master/examples).
- [Självstudier](https://github.com/Azure/azurehpc/tree/master/tutorials) om hur du enkelt kommer igång med några vanliga programarbetsflöden.

## <a name="optimally-scaling-mpi"></a>Optimal skalning av MPI 

Följande förslag gäller för optimal skalning av programeffektivitet, prestanda och konsekvens:

- För mindre skalningsjobb (d.v.s. < 256 000 anslutningar) använder du alternativet:
   ```bash
   UCX_TLS=rc,sm
   ```

- För jobb i större skala (d.v.s. > 256 000 anslutningar) använder du alternativet:
   ```bash
   UCX_TLS=dc,sm
   ```

- Om du vill beräkna antalet anslutningar för MPI-jobbet ovan använder du:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Anpassningsbar routning
Med anpassningsbar routning (AR) kan Azure Virtual Machines (VM) som kör EDR och ROUT InfiniBand automatiskt identifiera och undvika överbelastning genom att dynamiskt välja mer optimala nätverkssökvägar. Därför erbjuder AR bättre svarstid och bandbredd i InfiniBand-nätverket, vilket i sin tur ger högre prestanda och skalningseffektivitet. Mer information finns i [TechCommunity-artikeln](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Processnålning

- Fäst processer på kärnor med hjälp av en sekventiell metod för att fästa (till skillnad från en metod för automatisk balans). 
- Bindning från Numa/Core/HwThread är bättre än standardbindning.
- För hybridparallella program (OpenMP + MPI) använder du 4 trådar och 1 MPI-rangordning per CCX på HB- och HBv2 VM-storlekar.
- För rena MPI-program kan du experimentera med 1–4 MPI-rangordningar per CCX för optimal prestanda på STORLEKAR för virtuella datorer med HB och HBv2.
- Vissa program med extrem känslighet för minnesbandbredd kan dra nytta av att använda ett minskat antal kärnor per CCX. För dessa program kan användningen av 3 eller 2 kärnor per CCX minska minnesbandbredden och ge högre verkliga prestanda eller mer konsekvent skalbarhet. I synnerhet kan MPI Allreduce dra nytta av den här metoden.
- För betydligt större skalningskörningar rekommenderar vi att du använder UD- eller hybrid RC+UD-transporter. Många MPI-bibliotek/körningsbibliotek gör detta internt (till exempel UCX eller MVAPICH2). Kontrollera dina transportkonfigurationer för storskaliga körningar.

## <a name="compiling-applications"></a>Kompilera program
<br>
<details>
<summary>Klicka för att expandera</summary>

Även om det inte behövs ger kompilering av program med lämpliga optimeringsflaggor bästa möjliga uppskalningsprestanda på virtuella datorer i HB- och HC-serien.

### <a name="amd-optimizing-cc-compiler"></a>AMD optimerar C/C++-kompilatorn

Kompilatorsystemet AMD Optimizing C/C++ Compiler (AOCC) erbjuder en hög nivå av avancerade optimeringar, flertråds- och processorstöd som omfattar global optimering, vektorisering, inter procedurell analys, looptransformationer och kodgenerering. Binärfiler för AOCC-kompilatorer är lämpliga för Linux-system som har GNU C Library (rnbc) version 2.17 och senare. Kompilatorn består av en C/C++-kompilator (script), en Fortran-kompilator (FLANG) och en Fortran-frontend till Scriptg (Dragon Script).

### <a name="clang"></a>Klang

Ctrlg är en C-, C++- och Objective-C-kompilator som hanterar förbearbetning, parsning, optimering, kodgenerering, sammansättning och länkning. På Så här fungerar flaggan  `-march=znver1` för bästa möjliga kodgenerering och justering för AMD:s X86-baserade x86-arkitektur.

### <a name="flang"></a>FLANG

FLANG-kompilatorn är ett nytt tillägg till AOCC-sviten (lades till i april 2018) och finns för närvarande i en förhandsutgåvode där utvecklare kan ladda ned och testa. Baserat på Fortran 2008 utökar AMD GitHub-versionen av FLANG ( https://github.com/flang-compiler/flang) . FLANG-kompilatorn har stöd för alla Alternativ för kompilerare för Görg och ytterligare ett antal FLANG-specifika kompileringsalternativ.

### <a name="dragonegg"></a>DragonEgg

DragonEgg är ett gcc-plugin-program som ersätter GCC:s optimerare och kodgeneratorer med dem från LLVM-projektet. DragonEgg som medföljer AOCC fungerar med gcc-4.8.x, har testats för x86-32/x86-64-mål och har använts på olika Linux-plattformar.

GFortran är den faktiska frontend för Fortran-program som ansvarar för förbearbetning, parsning och semantisk analys som genererar GCC GIMPLE-representationen (IR). DragonEgg är ett GNU-plugin-program som ansluts till GFortran-kompileringsflödet. Den implementerar GNU-plugin-API:et. Med plugin-arkitekturen blir DragonEgg kompilatordrivrutinen som driver de olika faserna i kompileringen.  När du har följt nedladdnings- och installationsanvisningarna kan Du anropa Dragon Dragon med hjälp av: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI-kompilator
PGI Community Edition ver. 17 har bekräftats fungera med AMD EPYC. En PGI-kompilerad version av STREAM levererar plattformens fullständiga minnesbandbredd. Den nyare Community Edition 18.10 (nov 2018) bör också fungera bra. Nedan visas CLI-exempel för att kompilera optimalt med Intel-kompilatorn:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel Compiler ver. 18 har bekräftats att fungera med AMD EPYC. Nedan visas CLI-exempel för att kompilera optimalt med Intel Compiler.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-kompilator 
För HPC rekommenderar AMD GCC-kompilatorn 7.3 eller nyare. Äldre versioner, till exempel 4.8.5 som ingår i RHEL/CentOS 7.4, rekommenderas inte. GCC 7.3 och senare ger betydligt högre prestanda på HPL-, HPCG- och DGEMM-tester.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Nästa steg

- Testa dina kunskaper med en [utbildningsmodul om hur du optimerar HPC-program i Azure.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Läs [översikten över HBv3-serien och](hbv3-series-overview.md) [översikten över HC-serien.](hc-series-overview.md)
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat på [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Läs mer om [HPC](/azure/architecture/topics/high-performance-computing/) på Azure.
