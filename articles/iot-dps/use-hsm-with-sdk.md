---
title: Instruções do Azure - Como usar mecanismo de atestado diferentes com o SDK do Cliente de Serviço de Provisionamento de Dispositivos no Azure
description: Instruções do Azure - Como usar mecanismo de atestado diferentes com o SDK do Cliente de Serviço de Provisionamento de Dispositivos no Azure
author: yzhong94
ms.author: yizhon
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.custom: mvc
ms.openlocfilehash: af59ccc6d14dce49d06e178aac3ecafc29bd982c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248123"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Como usar mecanismo de atestado diferentes com o SDK do Cliente de Serviço de Provisionamento de Dispositivos para C

Este artigo mostra como usar diferentes [mecanismos de atestado](concepts-security.md#attestation-mechanism) com o SDK de cliente do Serviço de Provisionamento de Dispositivos para C. Você também pode usar um dispositivo físico ou um simulador. O serviço de provisionamento dá suporte à autenticação de dois tipos de mecanismos de Atestado: X. 509 e Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Pré-requisitos

Prepare o ambiente de desenvolvimento de acordo com a seção "Preparar o ambiente de desenvolvimento" no guia [Criar e provisionar dispositivo simulado](./quick-create-simulated-device.md).

### <a name="choose-an-attestation-mechanism"></a>Escolher um mecanismo de atestado

Como um fabricante de dispositivos, primeiro você precisa escolher um mecanismo de atestado que seja baseado em um dos tipos com suporte. Atualmente, o [SDK de cliente do Serviço de Provisionamento de Dispositivos para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) oferece suporte aos seguintes mecanismos de atestado: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): O TPM é um padrão estabelecido para a maioria das plataformas de dispositivo baseadas em Windows, bem como alguns dispositivos baseados em Linux/Ubuntu. Como um fabricante de dispositivos, você pode escolher esse mecanismo de atestado caso tenha um desses sistemas operacionais em execução em seus dispositivos, e se estiver procurando um padrão estabelecido para mecanismos de atestado. Com chips do TPM, você só pode registrar cada dispositivo individualmente para o Serviço de Provisionamento de Dispositivos. Para fins de desenvolvimento, você pode usar o simulador do TPM no seu computador de desenvolvimento do Windows ou Linux.

- [X.509](https://cryptography.io/en/latest/x509/): Certificados x. 509 podem ser armazenados em chips relativamente mais recentes chamados [módulos de segurança de Hardware (HSM)](concepts-security.md#hardware-security-module). A Microsoft também está adiantada nisso, em chips RIoT ou DICE, que implementam os certificados X.509. Com chips X.509, você pode fazer registros em massa no portal. Ele também dá suporte a determinados OSes não Windows como embedOS. Para fins de desenvolvimento, o SDK do cliente do Serviço de Provisionamento de Dispositivos dá suporte a um simulador de dispositivo de X.509. 

Para saber mais, confira os [Conceitos de segurança](concepts-security.md) e [conceitos de provisionamento automático](/azure/iot-dps/concepts-auto-provisioning) do Serviço de Provisionamento de Dispositivos no Hub IoT.

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Habilitar a autenticação para mecanismos de atestado com suporte

O modo de autenticação do SDK (X **.** 509 ou TPM) deve estar habilitado para o simulador ou dispositivo físico antes que eles possam ser registrados no portal do Azure. Primeiro, navegue até a pasta raiz de azure-iot-sdk-c. Execute o comando especificado, dependendo do modo de autenticação escolhido:

### <a name="use-x509-with-simulator"></a>Usar X **.** 509 com simulador

O serviço de provisionamento é fornecido com o emulador DICE (Device Identity Composition Engine), que gera um certificado X **.** 509 para autenticar o dispositivo. Para habilitar a autenticação X **.** 509, execute o comando a seguir: 

```
cmake -Ddps_auth_type=x509 ..
```

Informações sobre hardware com DICE podem ser encontradas [aqui](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Usar X **.** 509 com hardware

O serviço de provisionamento pode ser usado com X **.** 509 em outro hardware. Uma interface entre o hardware e o SDK é necessária para estabelecer conexão. Fale com o fabricante do HSM para obter informações sobre a interface.

### <a name="use-tpm"></a>Usar TPM

O serviço de provisionamento pode se conectar a chips de TPM de hardware Windows e Linux com token SAS. Para habilitar a autenticação do TPM, execute o comando a seguir:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Usar o TPM com simulador

Se você não tiver um dispositivo com chips TPM, poderá usar um simulador para fins de desenvolvimento no sistema operacional Windows. Para habilitar a autenticação do TPM e executar o simulador do TPM, execute o comando a seguir:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Criar o SDK
Crie o SDK antes de criar o registro do dispositivo.

### <a name="linux"></a>Linux
- Para criar o SDK do Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Para criar Binários de depuração, adicione a opção do CMake correspondente ao comando de geração de projeto, por exemplo:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Existem várias [opções de configuração do CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) disponíveis para criar o SDK. Por exemplo, você pode desabilitar uma das pilhas de protocolo disponível adicionando um argumento ao comando de geração de projeto do CMake:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Você também pode compilar e executar o teste de unidade:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a> Windows
- Para criar o SDK do Windows, siga estas etapas para gerar arquivos de projeto:
  - Abra o “Prompt de comando do desenvolvedor para VS2015”
  - Execute os seguintes comandos do CMake na raiz do repositório:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Esse comando cria bibliotecas de x86. Para compilar para x64, modifique o argumento do gerador de cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Se a geração de projeto for concluída com êxito, você deverá ver um arquivo de solução do Visual Studio (.sln) na pasta `cmake`. Para construir o SDK:
    - Abra **cmake\azure_iot_sdks.sln** no Visual Studio e compile-o, **ou**
    - Execute o seguinte comando no prompt de comando usado para gerar os arquivos de projeto:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Para criar Binários de depuração, use o argumento correspondente do MSBuild: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Existem várias opções de configuração do CMake disponíveis para criar o SDK. Por exemplo, você pode desabilitar uma das pilhas de protocolo disponível adicionando um argumento ao comando de geração de projeto do CMake:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Você também pode compilar e executar o teste de unidade:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotecas a incluir
- Essas bibliotecas devem ser incluídas em seu SDK:
    - O serviço de provisionamento: dps_http_transport, dps_client, dps_security_client
    - Segurança do IoTHub: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Criar uma entrada de registro de dispositivo no Serviço de Provisionamento de Dispositivos

### <a name="tpm"></a>TPM
Se você estiver usando o TPM, siga as instruções em ["Criar e provisionar um dispositivo simulado usando o Serviço de Provisionamento de Dispositivos no Hub IoT"](./quick-create-simulated-device.md) para criar uma entrada de registro de dispositivo no Serviço de Provisionamento de Dispositivos e simular a primeira inicialização.

### <a name="x509"></a>X **.** 509

1. Para registrar um dispositivo no serviço de provisionamento, você precisa anotar a Chave de Endosso e a ID de Registro para cada dispositivo, que são exibidas na Ferramenta de Provisionamento fornecida pelo SDK do Cliente. Execute o seguinte comando para imprimir o certificado de Autoridade de Certificação raiz (para grupos de registro) e o certificado da folha (para registro individual):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Entre no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.
   - X **.** Registro individual 509: Sobre a folha de resumo de serviço de provisionamento, selecione **gerenciar registros**. Selecione a guia **Registros Individuais** guia e clique no botão **Adicionar** na parte superior. Selecione **X**. **509** como o *Mecanismo* de atestado de identidade e carregue o certificado do signatário conforme exigido pela folha. Uma vez concluído, clique no botão **Salvar**. 
   - X **.** Registro de grupo 509: Sobre a folha de resumo de serviço de provisionamento, selecione **gerenciar registros**. Selecione a guia **Registros em grupo** e clique no botão **Adicionar** na parte superior. Selecione **X**. **509** como o *Mecanismo* de atestado de identidade, insira o nome do grupo e o nome da certificação e carregue o certificado de Autoridade de Certificação/Intermediário conforme exigido pela folha. Uma vez concluído, clique no botão **Salvar**. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Habilitar a autenticação para dispositivos usando um mecanismo de atestado personalizado (opcional)

> [!NOTE]
> Esta seção é aplicável somente a dispositivos que exigem suporte para uma plataforma ou mecanismos de atestado personalizados, atualmente não há suporte para o DK de Cliente do Serviço de Provisionamento de Dispositivos para C. Observe também que o SDK frequentemente usa o termo “HSM” como um substituto genérico no lugar de “mecanismo de atestado”.

Primeiro você precisa desenvolver um repositório e a biblioteca para o seu mecanismo de atestado personalizado:

1. Desenvolver uma biblioteca para acessar seu mecanismo de atestado. Este projeto deve produzir uma biblioteca estática para o SDK de provisionamento de dispositivos consumir.

2. Implemente as funções definidas no arquivo de cabeçalho abaixo, na sua biblioteca: 

    - Para um TPM personalizado: implemente as funções definidas na [API HSM TPM](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - Para um X.509 personalizado: implemente as funções definidas em [API HSM X.509](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Depois que a biblioteca se cria sozinha com êxito, você precisa integrá-la ao SDK de cliente do Serviço de Provisionamento de Dispositivos vinculando-a à sua biblioteca. :

1. Forneça o repositório GitHub personalizado e a biblioteca no comando `cmake`:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Abra o arquivo de solução do Visual Studio criado pelo CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) e compile-o. 

    - O processo de compilação cria a biblioteca do SDK.
    - O SDK tentará se vincular à biblioteca personalizada definida no comando `cmake`.

3. Para verificar se o seu mecanismo de atestado personalizado foi implementado corretamente, execute o aplicativo de exemplo "prov_dev_client_ll_sample" em "Provision_Samples" (em `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`).

## <a name="connecting-to-iot-hub-after-provisioning"></a>Conectar-se ao Hub IoT após a configuração

Depois que o dispositivo foi configurado com o serviço de provisionamento, essa API usará o modo de autenticação (X **.** 509 ou TPM) para se conectar ao Hub IoT: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
