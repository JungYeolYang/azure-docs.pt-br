---
title: Usar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionar dispositivos entre Hubs IoT com balanceamento de carga | Microsoft Docs
description: Provisionamento automático de dispositivos do serviço de provisionamento de dispositivos entre Hubs IoT com balanceamento de carga no Portal do Azure
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 887bda92a1165a3dd17e9105e921a5df9e0c5534
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248157"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Provisionar dispositivos entre Hubs IoT com balanceamento de carga

Este tutorial mostra como provisionar dispositivos para vários Hubs IoT com balanceamento de carga usando o Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT 
> * Adicionar uma entrada da lista de registro para o segundo dispositivo
> * Definir a política de alocação no Serviço de Provisionamento de Dispositivos como **distribuição por igual**
> * Vincular o novo Hub IoT ao Serviço de Provisionamento de Dispositivos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial baseia-se no tutorial anterior [Provisionar o dispositivo em um hub](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT

Siga as etapas no tutorial [Provisionar o dispositivo em um hub](tutorial-provision-device-to-hub.md) para provisionar um segundo dispositivo em outro Hub IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Adicionar uma entrada da lista de registro para o segundo dispositivo

A lista de registro informa ao Serviço de Provisionamento de Dispositivos qual método de atestado (o método para confirmar a identidade de um dispositivo) ele está usando com o dispositivo. A próxima etapa é adicionar uma entrada da lista de registro para o segundo dispositivo. 

1. Na página do Serviço de Provisionamento de Dispositivo, clique em **Gerenciar registros**. A página **Adicionar entrada da lista de registro** será exibida. 
2. Na parte superior da página, clique em **Adicionar**.
2. Preencha os campos e, em seguida, clique em **Salvar**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Definir a política de alocação no Serviço de Provisionamento de Dispositivos

A política de alocação é uma configuração do Serviço de Provisionamento de Dispositivos que determina como os dispositivos são atribuídos a um Hub IoT. Há três políticas de alocação com suporte: 

1. **Menor latência**: Os dispositivos são provisionados para um Hub IoT com base no hub com a menor latência para o dispositivo.
2. **Distribuição uniformemente ponderada** (padrão): Hubs IoT vinculados têm probabilidades iguais de ter dispositivos provisionados a eles. Esta é a configuração padrão. Se estiver provisionando dispositivos a apenas um Hub IoT, você poderá manter essa configuração. 
3. **Configuração estática através da lista de registro**: A especificação do Hub IoT desejado na lista de registro tem prioridade sobre a política de alocação no nível do Serviço de Provisionamento de Dispositivos.

Siga estas etapas para definir a política de alocação:

1. Para definir a política de alocação, na página do Serviço de Provisionamento de Dispositivos, clique em **Gerenciar política de alocação**.
2. Defina a política de alocação para **Distribuição uniformemente ponderada**.
3. Clique em **Save** (Salvar).

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Vincular o novo Hub IoT ao Serviço de Provisionamento de Dispositivos

Vincule o Serviço de Provisionamento de Dispositivos e o Hub IoT para que o Serviço de Provisionamento de Dispositivos possa registrar dispositivos nesse hub.

1. Na página **Todos os recursos**, clique no Serviço de Provisionamento de Dispositivos que você criou anteriormente.
2. Na página do Serviço de Provisionamento de Dispositivos, clique em **Hubs IoT Vinculados**.
3. Clique em **Adicionar**.
4. Na página **Adicionar link para o Hub IoT**, use os botões de opção para especificar se o Hub IoT vinculado está localizado na assinatura atual ou em uma diferente. Em seguida, escolha o nome do hub IoT da caixa **Hub IoT**.
5. Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT 
> * Adicionar uma entrada da lista de registro para o segundo dispositivo
> * Definir a política de alocação no Serviço de Provisionamento de Dispositivos como **distribuição por igual**
> * Vincular o novo Hub IoT ao Serviço de Provisionamento de Dispositivos

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
