---
title: 'Criar e instalar arquivos de configuração de cliente VPN P2S para autenticação de certificado do Azure: Azure'
description: Crie e instale arquivos de configuração do cliente VPN do Windows, Linux (strongSwan) e Mac OS X para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: cherylmc
ms.openlocfilehash: b590dabbe4b2c6526f2c602aeed64667348eefa9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525160"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar arquivos de configuração de cliente VPN para configurações P2S da autenticação de certificado nativa do Azure

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para um cliente VPN nativo do Windows, Mac IKEv2 ou Linux para conectar-se a uma rede virtual Ponto a Site que usa a autenticação de certificado nativa do Azure.

Os arquivos de configuração do cliente são específicos para a configuração de VPN da VNet. Se houver alterações na configuração de VPN Ponto a Site depois de gerar os arquivos de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, gere e instale novos arquivos de configuração de cliente VPN nos dispositivos do usuário. 

* Para saber mais sobre conexões Ponto a Site, confira [Sobre VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções sobre o OpenVPN, confira [Configurar o OpenVPN para P2S](vpn-gateway-howto-openvpn.md) e [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Gerar os arquivos de configuração de cliente VPN

Antes de começar, verifique se todos os usuários conectados têm um certificado válido instalado no dispositivo do usuário. Para saber mais sobre como instalar um certificado de cliente, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

É possível gerar arquivos de configuração do cliente usando o PowerShell ou usando o Portal do Azure. O método retorna o mesmo arquivo zip. Descompacte o arquivo para ver as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes dos instaladores do Windows de 32 bits e 64 bits, respectivamente. O pacote do instalador de **WindowsAmd64** serve para todos os clientes do Windows de 64 bits, não apenas ao Amd.
  * **Generic**, que contém informações gerais, usadas para criar sua própria configuração de cliente VPN. A pasta Genérico será fornecida se IKEv2 ou SSTP+IKEv2 tiver sido configurado no gateway. Se apenas o SSTP estiver configurado, a pasta Genérico não estará presente.

### <a name="zipportal"></a>Gerenciar arquivos usando o Portal do Azure

1. No Portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
2. Na página de gateway de rede virtual, clique em **Configuração Ponto a Site**.
3. Na parte superior da página da configuração ponto a site, clique em **Baixar cliente VPN**. Levará alguns minutos para o pacote de configuração do cliente ser gerado.
4. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele terá o mesmo nome do seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="zipps"></a>Gerar arquivos usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente de VPN usando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie a URL para o seu navegador para baixar o arquivo zip. Em seguida, descompacte o arquivo para exibir as pastas.

## <a name="installwin"></a>Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais quer recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows a partir do qual deseja conectar-se.
>
>

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 
4. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Você precisa configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que se conecta ao Azure. O Azure não oferece arquivo mobileconfig para autenticação de certificado do Azure nativa. A **Generic** contém todas as informações que você precisa para configuração. Caso não veja a pasta Genérico em seu download, talvez o IKEv2 não tenha sido selecionado como um tipo de túnel. Observe que o gateway de VPN SKU básica não dá suporte a IKEv2. Depois que o IKEv2 for selecionado, gere o arquivo zip novamente para recuperar a pasta Genérico.<br>A pasta Genérico contém os seguintes arquivos:

* **VpnSettings.xml**, que contém configurações importantes, como o tipo de túnel e o endereço do servidor. 
* **VpnServerRoot.cer**, que contém o certificado raiz necessário para validar o Gateway de VPN do Azure durante a instalação de conexão P2S.

Use as seguintes etapas para configurar o cliente VPN nativo do Mac para autenticação de certificado. Você precisa concluir estas etapas em cada Mac que se conecta ao Azure:

1. Importe o certificado raiz **VpnServerRoot** para seu Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele. Clique em **Adicionar** para importar.

   ![adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Ao clicar duas vezes na caixa de diálogo **Adicionar**, o certificado poderá não ser exibido, mas será instalado no repositório correto. Você pode verificar o certificado no conjunto de chaves de logon, na categoria Certificados.
    >
  
2. Verifique se você instalou um certificado do cliente emitido pelo certificado raiz que você carregou no Azure quando definiu as configurações de P2S. Ele é diferente do VPNServerRoot que você instalou na etapa anterior. O certificado do cliente é usado para autenticação e é necessário. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Abra a caixa de diálogo **Rede** em **Preferências de rede** e clique em **'+'** para criar um novo perfil de conexão de cliente VPN para uma conexão de P2S para a rede virtual do Azure.

   O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **Nome do serviço** e, em seguida, clique em **Criar** para criar o perfil de conexão de cliente VPN.

   ![rede](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil.

   ![informações do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **Configurações de autenticação** e selecione **Certificado**. 

   ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Clique em **Selecionar...** para escolher o certificado que deseja usar para autenticação. Trata-se do certificado que você instalou na etapa 2.

   ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, clique em **Continuar**.

   ![identidade](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. No campo **ID local**, especifique o nome do certificado (da Etapa 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para salvar as alterações.

   ![aplicar](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na caixa de diálogo **Rede**, clique em **Aplicar** para salvar todas as alterações. Em seguida, clique em **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="linuxgui"></a>Linux (GUI strongSwan)

### <a name="extract-the-key-and-certificate"></a>Extrair a chave e o certificado

Para strongSwan, é necessário extrair a chave e o certificado do certificado do cliente (arquivo .pfx) e salvá-los em arquivos .pem individuais.
Siga as etapas abaixo:

1. Baixe e instale o OpenSSL do [OpenSSL](https://www.openssl.org/source/).
2. Abra uma janela de linha de comando e vá para o diretório em que o OpenSSL está instalado, por exemplo, 'c:\OpenSLL-Win64\bin\'.
3. Execute o comando a seguir para extrair a chave privada e salve-a em um novo arquivo chamado 'privatekey.pem' do seu certificado de cliente:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
   ```
4. Agora, execute o comando a seguir para extrair o certificado público e salvá-lo em um novo arquivo:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
   ```

### <a name="install"></a>Instalar e configurar

As instruções a seguir foram criadas por meio do forteSwan 5.5.1 no Ubuntu 17.0.4. O Ubuntu 16.0.10 não dá suporte para GUI do StrongSwan. Se você quiser usar o Ubuntu 16.0.10, será necessário usar a [linha de comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder às telas que você vê, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **Terminal** para instalar o **strongSwan** e seu Gerenciador de Rede executando o comando no exemplo. Se você receber um erro relacionado a *libcharon-extra-plugins*, substitua-o por 'strongswan-plugin-eap-mschapv2'.

   ```
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o ícone **Gerenciador de Rede** (seta para cima/seta para baixo) e, em seguida, selecione **Editar Conexões**.

   ![editar conexões](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique no botão **Adicionar** para criar uma nova conexão.

   ![adicionar um conexão](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **IPsec/IKEv2 (strongswan)** no menu suspenso e, em seguida, clique em **Criar**. Você pode renomear sua conexão nesta etapa.

   ![escolher um tipo de conexão](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o arquivo **VpnSettings.xml** da pasta **Genérico** contida nos arquivos de configuração do cliente baixados. Localize a marca chamada **VpnServer** e copie o nome, iniciando com "azuregateway" e finalizando com ". cloudapp.net".

   ![copiar nome](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Cole esse nome no campo **Endereço** da sua nova conexão VPN na seção **Gateway**. Em seguida, selecione o ícone da pasta no final do campo **Certificado**, navegue até a pasta **Genérico** e selecione o arquivo **VpnServerRoot**.
7. Na seção **Cliente** da conexão, da **Autenticação**, selecione **Certificado/chave privada**. Para **Certificado** e **Chave privada**, escolha o certificado e a chave privada que foram criados anteriormente. Em **Opções**, selecione **Solicitar um endereço IP interno**. Em seguida, clique em **Adicionar**.

   ![solicitar um endereço IP interno](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Clique no ícone do **Gerenciador de rede** (seta para cima/seta para baixo) e passe o mouse sobre **Conexões VPN**. Você visualiza a conexão VPN que foi criada. Clique para iniciar a conexão.

## <a name="linuxinstallcli"></a>Linux (CLI do strongSwan)

### <a name="install-strongswan"></a>Instalar o strongSwan

Para instalar o strongSwan, você pode usar os seguintes comandos da CLI ou usar as etapas do strongSwan na [GUI](#install).

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Instalar e configurar

1. Baixe o pacote de VPNClient do portal do Azure.
2. Extraia o arquivo.
3. Da pasta **Generic**, copie ou mova o VpnServerRoot.cer para /etc/ipsec.d/cacerts.
4. Copie ou mova o cp client.p12 para /etc/ipsec.d/private/. Este arquivo é um certificado de cliente para o Gateway de VPN do Azure.
5. Abra o arquivo VpnSettings.xml e copie o valor `<VpnServer>`. Você usará esse valor na próxima etapa.
6. Ajuste os valores no exemplo a seguir e adicione o exemplo à configuração /etc/ipsec.conf.
  
   ```
   conn azure
   keyexchange=ikev2
   type=tunnel
   leftfirewall=yes
   left=%any
   leftauth=eap-tls
   leftid=%client # use the DNS alternative name prefixed with the %
   right= Enter the VPN Server value here# Azure VPN gateway address
   rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
   rightsubnet=0.0.0.0/0
   leftsourceip=%config
   auto=add
   ```
6. Adicione o conteúdo a seguir a */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Execute os seguintes comandos:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Para solucionar problemas de conexões P2S, veja os seguintes artigos:

  * [Solução de problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Solucionar problemas de conexões VPN de clientes VPN do Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
