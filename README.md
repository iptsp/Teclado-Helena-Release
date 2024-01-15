# Projeto Teclado Helena

## Como posso instalar para testar em casa?
Para utilizar o Teclado Helena, você precisa ter um computador ou notebook com o Windows instalado e um dispositivo com tela sensível ao toque, como um tablet, que servirá como o teclado.
Para instalar essa versão de testes, é necessário instalar o certificado de desenvolvimento.
[O que é um certificado de desenvolvimento?](#O-que-é-um-certificado-de-desenvolvimento?).

### Instalando o certificado
Esse arquivo tem o nome de **`THL.pfx`**. [Clique aqui para baixar!](https://github.com/iptsp/Teclado-Helena-Release/releases/download/pre-release/THL.pfx)

- No Windows, de um duplo clique sobre ele,
- Na janela que abrirá, selecione a opção **`Máquina Local`** e clique em **`Avançar`**,
- Na janela de permissão, clique em **`Sim`**,
- Na próxima janela *"Arquivo a ser importado"*, clique em **`Avançar`**,
- Na senha, digite **`!thl!321#`** e cliquem em **`Avançar`**,
- Na próxima janela *"Respositório de Certificados"*, clique em **`Colocar todos os certificados no repositório a seguir`**,
- Clique em **`Procurar...`**, selecione a opção **Pessoas Confiáveis**, clique em **`OK`**,
- Clique em **`Avançar`** e na janela seguinte clique em **`Concluir`**.
- Uma mensagem irá aparecer dizendo que foi instalado corretamente. Basta clicar em **`OK`**.

Pronto! 
O certificado de testes foi instalado, agora podemos instalar o Teclado Helena.

### Instalando o Teclado Helena
O arquivo de instalação tem o nome de **`THL-0.2.msix`**. [Clique aqui para baixar!](https://github.com/iptsp/Teclado-Helena-Release/releases/download/pre-release-v0.2/THL-0.2.msix)

- No Windows, de um duplo clique sobre ele,
- Na janela que abrirá, clique em **`Instalar`**,
- O processo de instalação se iniciará. É rápido!
- Finalizando a instalação, a janela se fechará e o Teclado Helena será iniciado!

### Usando o Teclado Helena
No Windows, uma janela com um **QR Code** irá abrir em sua tela.
Isso significa que o Teclado Helena está pronto para uso!

No tablet, abra o **aplicativo de camera** do seu celular e **aponte** a camera para o **QR Code**.
Será exibido uma opção para acessar como link, selecione essa opção e em seguida selecione o navegador de sua preferência.

O Teclado Helena deverá abrir na tela de seu tablet.
Agora está pronto para uso!


------------


## Curiosidades
### O que é um certificado de desenvolvimento?
É um arquivo que instalamos no computador para ele entender que esse programa não traz riscos para o sistema, porém ele é de desenvolvimento, ou seja, ainda não é reconhecido.
Portanto, recomendamos que utilize esse certificado apenas para testes, e depois remova do seu computador.

### Como funciona?
O Teclado Helena é instalado no Windows e acessado pelo tablet no navegador (Ex.: Google Chrome ou Firefox).
Ao instalar e abrir o Teclado Helena, um QRCode é exibido na tela.
No tablet, abrindo o aplicativo de camera, é possível apontar-la para o QRCode no qual fará a leitura do endereço.
Logo, basta clicar na opção "Abrir no navegador", escolher seu navegador de preferencia caso necessário e o Teclado Helena irá carregar no tablet.
Pronto!
Agora só utilizar!

# Para desenvolvedores

## Pré-requisitos
Para rodar e compilar esse projeto, as seguintes dependências devem ser instaladas em seu Windows:
* Java 21 JDK (https://jdk.java.net/21/)
* Maven 3.x (https://maven.apache.org/download.cgi)
* Node 20.x (https://nodejs.org/en/download/)
* Windows SDK (https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)
* Git (https://git-scm.com/downloads)

## Desenvolvimento
### Executando o cliente
Em um terminal, execute os comando abaixo, estando na **pasta raiz** do projeto:
```
cd client/ui
npm install
npm run start
```

### Executando o servidor
Importe o repositório como um projeto Maven na sua IDE de preferência (Ex.: IntelliJ, VSCode, Eclipse)
Execute a classe ```br.ipt.thl.Entrypoint``` como uma aplicação JAVA.

Habilite o **`vm options`** e coloque as seguintes opções: 
```-Dthl.version=dev -Dspring.profiles.active=dev -Djavafx.preloader=br.ipt.thl.UiApplicationSplashScreen -Xms512m -Xmx1g```

## Compilação
Ao compilar o projeto será gerado um pacote MSIX que pode ser usado como instalador para o Windows.

Como regra geral, todos os pacotes MSIX devem ser assinados com um certificado válido de assinatura de código antes de poderem ser instalados.

Para fins de desenvolvimento, um certificado autoassinado está incluído neste repositório.
Se for necessário gerar outro certificado autoassinado, consulte [Gerando um certificado de assinatura de código autoassinado](#Gerando-um-certificado-de-assinatura-de-código-autoassinado)
If there is a need to generate another self-signed certificate, refer to [Generating a self-signed code signing certificate](#Generating-a-self-signed-code-signing-certificate).

> [!AVISO]
> O certificado autoassinado incluído destina-se apenas para fins de desenvolvimento. Não deve ser utilizado em produção.

1. Garanta que as ferramentas `signtool.exe`, `makeappx.exe` e `makepri.exe` estejam no **PATH**. 
Estas são instaladas com o **Windows SDK** e podem ser encontradas em `C:\Program Files (x86)\Windows Kits\10\bin<win build>\x64`.

2. Garanta que o certificado de assinatura de código esteja instalado no repositório de certificados da Máquina Local.
Para instruções detalhadas sobre como instalar o certificado autoassinado incluído, consulte [Instalando o certificado autoassinado incluído](#Instalando-o-certificado-autoassinado-incluído).

3. Atualize a propriedade `certificate.fingerprint` no arquivo `server\pom.xml` para corresponder à impressão digital do certificado de assinatura de código instalado.
Se estiver utilizando o certificado autoassinado incluído, este valor não precisa ser alterado.

4. Como administrador, execute ```mvn clean install```. 
Executar sem privilégios administrativos impedirá a ferramenta de assinatura de encontrar o certificado.

> [!IMPORTANTE]
> Se o pacote MSIX foi assinado com um certificado autoassinado, o certificado também deve ser instalado na máquina de destino.
>
> Consulte [Instalando um certificado autoassinado de um pacote MSIX](#Instalando-um-certificado-autoassinado-de-um-pacote-MSIX).

***

## Etapas opcionais

### Gerando um certificado de assinatura de código autoassinado

O processo de compilação requer um certificado de assinatura de código para assinar os executáveis e o pacote MSIX.

Se um certificado de assinatura de código válido não estiver disponível, um certificado autoassinado pode ser criado por meio dos passos abaixo para fins de desenvolvimento.

Execute os seguintes comandos em um terminal do PowerShell:

```
New-SelfSignedCertificate -Type Custom -Subject "CN=THL, O=IPT, C=BR" -KeyUsage DigitalSignature -FriendlyName "THL Certificate" -CertStoreLocation "Cert:\CurrentUser\My" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}")
$password = ConvertTo-SecureString -String "!thl!321#" -Force -AsPlainText
$thumbprint = (Get-ChildItem -Path Cert:\CurrentUser\My | Where-Object {$_.Subject -eq "CN=THL, O=IPT, C=BR"} |Sort-Object LastWriteTime -Descending | Select-Object -first 1).Thumbprint
$user_home = [Environment]::GetFolderPath("UserProfile")
Export-PfxCertificate -Cert "Cert:\CurrentUser\My\$thumbprint" -FilePath "$user_home\THL.pfx" -Password $password
```

Copie o arquivo PFX gerado de `$user_home\THL.pfx` para `server\jpackage\certificate` na pasta do projeto.

### Instalando o certificado autoassinado incluído

<details open>
<summary><b>Opção 1: Pelo CLI</b></summary>

1. Como administrador, execute em um PowerShell o seguinte comando:
   ```
   Import-PfxCertificate -FilePath '.\server\jpackage\certificate\THL.pfx' -CertStoreLocation 'Cert:\LocalMachine\My' -Password (ConvertTo-SecureString '!thl!321#' -AsPlainText -Force)
   ```
</details>

<details open>
<summary><b>Opção 2: Pela Interface Gráfica (GUI)</b></summary>

1. Dê um duplo clique no arquivo `server\jpackage\certificate\THL.pfx` para abrir o Assistente de Importação de Certificado.
2. Selecione **`Máquina Local`** como local do repositório, clique em **`Avançar`**.
3. Clique em **`Avançar`**.
4. Digite `!thl!321#` como senha, clique em **`Avançar`**.
5. Selecione `Selecionar automaticamente o repositório de certificados com base no tipo de certificado`, clique em **`Avançar`**.
6. Clique em **`Concluir`**.

</details>

### Instalando o certificado autoassinado incluído

Para instalar pacotes MSIX que foram assinados com um certificado autoassinado, é necessário confiar nessa assinatura instalando o certificado no repositório de certificados ```Cert:\LocalMachine\TrustedPeople``` certificate store.

> [!AVISO]

> Instalar um certificado autoassinado no Windows fará com que o sistema confie em todos os pacotes assinados com esse certificado, o que pode incluir alguns maliciosos.
> 
> É altamente recomendável desinstalar o certificado após os testes para garantir a segurança do seu sistema.

<details open>
<summary><b>Opção 1: Pelo CLI</b></summary>

1. Execute o seguinte comando de um prompt do PowerShell com privilégios de administrador. 
Substitua ```THL-0.2.msix``` pelo caminho para o arquivo MSIX.
   ```
   Get-AuthenticodeSignature 'THL-0.2.msix' | Select-Object -ExpandProperty SignerCertificate | Export-Certificate -FilePath ($TempFile = New-TemporaryFile).FullName; Import-Certificate -FilePath $TempFile.FullName -CertStoreLocation 'Cert:\LocalMachine\TrustedPeople'; Remove-Item -Path $TempFile.FullName
   ```
</details>

<details open>
<summary><b>Opção 2: Pela Interface Gráfica (GUI)</b></summary>

1. Clique com o **botão direito** do mouse no arquivo `.msix` e vá até **`Propriedades`**.
2. Na aba Assinaturas Digitais (Digital Signatures), selecione o certificado THL e clique em **`Detalhes`**.
3. Na janela Detalhes de Assinatura Digital, clique em **`Exibir Certificado`**.
4. Na janela Certificado, clique em **`Instalar Certificado...`**
5. No Assistente de Importação de Certificado, selecione **`Máquina Local`** como local do repositório, clique em **`Avançar`**.
6. Selecione **`Colocar todos os certificados no seguinte repositório`** e clique em **`Procurar...`**
7. Selecione o repositório **`Pessoas Confiáveis`** e clique em **`OK`**.
8. Clique em **`Avançar`**.
9. Clique em **`Concluir`**.

</details>

***

# Documentos de referência

* Win32 Api Docs      (https://learn.microsoft.com/en-us/windows/win32/apiindex/api-index-portal)
* Java 21.x Docs      (https://docs.oracle.com/en/java/javase/21/docs/api/index.html)
* JavaFX 20.x Docs    (https://openjfx.io/javadoc/20/)
* Maven 3.x Docs      (https://maven.apache.org/guides/index.html)
* Git 2.x Docs        (https://git-scm.com/doc)
* Node 20.x Docs      (https://nodejs.org/dist/latest-v20.x/docs/api/)

# Licença

Teclado Helena is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

Teclado Helena is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License 
along with this program (COPYING.txt).  If not, see <https://www.gnu.org/licenses/>
