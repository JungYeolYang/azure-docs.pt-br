---
title: Kit de fala dispositivos SDK Roobo inteligente áudio Dev v1 - serviços de fala
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para a guia de Introdução com o SDK de dispositivos de fala, Roobo Smart áudio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1a13a44d9db8ed029ce798f0bb34944a1a65a7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409070"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: Kit de desenvolvimento de áudio Roobo inteligente

Este artigo fornece informações específicas de dispositivo para o Kit de Dev Roobo inteligente áudio.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores micro USB. O conector à esquerda serve para alimentar o kit de desenvolvimento e está realçado como Ligar/Desligar na imagem abaixo. O da direita serve para controlá-lo e está marcado como Depurar na imagem.

    ![Conectar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. Ligue o kit de desenvolvimento usando um cabo micro USB para conectar a porta de energia a um PC ou adaptador de energia. Um indicador de energia verde acenderá sob o quadro superior.

1. Para controlar o kit de desenvolvimento, conecte-se a porta de depuração para um computador por meio de um segundo micro cabo USB. É essencial usar um cabo de alta qualidade para garantir comunicações confiáveis.

1. Oriente seu kit de desenvolvimento para configuração circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Na vertical, com microfones voltada para o teto|
    |Linear|Em seu lado, com os microfones voltados para o usuário (mostrado na imagem a seguir)|

    ![Orientação do kit de desenvolvimento linear](media/speech-devices-sdk/qsg-2.png)

1. Instalar os certificados e defina as permissões do dispositivo de som. Digite os seguintes comandos em uma janela de Prompt de comando:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Esses comandos usam a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do adb.exe em cada comando que invoca `adb`.
    >
    > Se você vir um erro `no devices/emulators found` , em seguida, verificar o cabo USB está conectado e é um cabo de alta qualidade. Você pode usar `adb devices` para verificar se o computador pode se comunicar com o kit de desenvolvimento, já que ele retornará uma lista de dispositivos.
    >
    > [!TIP]
    > Coloque o microfone e alto-falante no mudo para certificar-se de que você está trabalhando com microfones do kit de desenvolvimento de seu PC. Dessa forma, você não dispara acidentalmente o dispositivo com o áudio do PC.

1. Se você quiser conectar um alto-falante ao o kit de desenvolvimento, você pode conectá-lo à saída de linha de Áudio. Você deve escolher um palestrante de boa qualidade com plugue analógico 3,5 mm.

    ![Áudio do Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informações de desenvolvimento

Para obter mais informações de desenvolvimento, consulte o [guia de desenvolvimento Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Áudio

Roobo fornece uma ferramenta que captura todos os áudio memória Flash. Isso pode ajudá-lo a solucionar problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. Sobre o [site Roobo](https://ddk.roobo.com/), selecione seu dispositivo e, em seguida, selecione o **Roobo ferramentas** link na parte inferior da página.

## <a name="next-steps"></a>Próximas etapas

* [Execute o aplicativo de exemplo do Android](speech-devices-sdk-android-quickstart.md)
