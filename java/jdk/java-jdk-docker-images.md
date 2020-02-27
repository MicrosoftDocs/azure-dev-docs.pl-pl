---
title: Używanie obrazów platformy Docker z zestawem JDK na potrzeby programowania w języku Java na platformie Azure
description: Dowiedz się, jak korzystać z obrazów platformy Docker z zestawem Java Development Kit (JDK) dla platformy Azure przy użyciu interfejsu wiersza polecenia.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b3a046563fa51efae32fd45e6154901539a8f4c8
ms.sourcegitcommit: 6ffa53b933da524e09911b164bba8515722bfa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77629372"
---
# <a name="use-docker-with-a-java-development-kit-jdk-for-azure"></a>Używanie platformy Docker z zestawem Java Development Kit (JDK) dla platformy Azure

W tym artykule opisano sposób korzystania z platformy Docker z zestawem Java Development Kit (JDK) dla platformy Azure. Wstępnie skompilowane obrazy platformy Docker dla języka Java w wersji 7, 8 i 11 są dostępne za pośrednictwem usługi [Docker Hub](https://hub.docker.com/_/microsoft-java-se).

Certyfikowane obrazy kontenerów platformy Docker dla środowisk Zulu JDK, JRE i JRE-headless dla wielu podstawowych obrazów systemów operacyjnych są dostępne w usłudze Docker Hub:

* Zestaw [JDK](https://hub.docker.com/_/microsoft-java-jdk).
* Środowisko [JRE](https://hub.docker.com/_/microsoft-java-jre)
* Środowisko [JRE-headless](https://hub.docker.com/_/microsoft-java-jre-headless)

## <a name="running-a-docker-image"></a>Uruchamianie obrazu platformy Docker

Obrazy platformy Docker można uruchamiać przy użyciu składni `$ docker run mcr.microsoft.com/java/jdk:tag java`, jak pokazano w poniższym przykładzie.

```cli
docker run mcr.microsoft.com/java/jdk:8-zulu-alpine java -version
```

## <a name="creating-a-docker-image"></a>Tworzenie obrazu platformy Docker

Obraz można utworzyć przy użyciu oficjalnych obrazów usługi Docker Hub firmy Microsoft, jak pokazano w poniższych przykładach.

### <a name="create-a-docker-file"></a>Tworzenie pliku platformy Docker

```cli
FROM mcr.microsoft.com/java/jdk:8-zulu-alpine
  
RUN echo $' \
  
public class HelloWorld { \
   public static void main(String[] args) { \
      // Prints "Hello, World" in the terminal window. \
      System.out.println("Hello, World - From Microsoft Azure !!!"); \
   } \
}' > HelloWorld.java
  
RUN javac HelloWorld.java
  
CMD ["java", "HelloWorld"]
```

### <a name="build-a-docker-image"></a>Kompilowanie obrazu platformy Docker

```cli
docker build -t hello-world
```

### <a name="run-the-new-image"></a>Uruchamianie nowego obrazu

```cli
docker run hello-world
```

Zostaną wyświetlone następujące dane wyjściowe:

```output
Hello World - From Microsoft Azure !!!
```
