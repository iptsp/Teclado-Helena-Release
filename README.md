# Projeto THL - Teclado Helena

## Pré-requisitos
Para executar e construir este projeto, as seguintes dependências devem estar instaladas em sua máquina:
* Java 21 JDK (https://jdk.java.net/21/)
* Maven 3.x (https://maven.apache.org/download.cgi)
* Node 20.x (https://nodejs.org/en/download/)
* Windows SDK (https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)
* Git (https://git-scm.com/downloads)

## Desenvolvimento
### Executando o cliente
```
cd client/ui
npm install
npm run start
```

### Executando o servidor
Importe este repositório como um projeto Maven em seu IDE preferido.

Execute a classe ```br.ipt.thl.Entrypoint``` como aplicação Java com opções de vm:
```-Dthl.version=dev -Dspring.profiles.active=dev -Djavafx.preloader=br.ipt.thl.UiApplicationSplashScreen -Xms512m -Xmx1g```

## Construção
Construir o projeto irá gerar um pacote MSIX que pode ser usado como instalador para computadores Windows.

Como regra geral, todos os pacotes MSIX devem ser assinados com um certificado válido de assinatura de código antes de poderem ser instalados.

Para fins de desenvolvimento, um certificado autoassinado está incluído neste repositório.
Se houver necessidade de gerar outro certificado autoassinado, consulte [Gerando um certificado de assinatura de código autoassinado_](#Gerando-um-certificado-de-assinatura-de-código-autoassinado).

> [AVISO!]
> O certificado autoassinado incluído destina-se apenas a fins de desenvolvimento. Não deve ser utilizado em produção.

1. Garanta que as ferramentas signtool.exe, makeappx.exe e makepri.exe estejam no PATH.
Estas são instaladas com o Windows SDK e podem ser encontradas em C:\Program Files (x86)\Windows Kits\10\bin\<win build>\x64.

2. Garanta que o certificado de assinatura de código esteja instalado no repositório de certificados da Máquina Local.
Para instruções detalhadas sobre como instalar o certificado autoassinado incluído, consulte [Instalando o certificado autoassinado incluído](#Instalando-o-certificado-autoassinado-incluído). 

3. Atualize a propriedade certificate.fingerprint no arquivo server\pom.xml para corresponder à impressão digital do certificado de assinatura de código instalado.
Se estiver utilizando o certificado autoassinado incluído, este valor não precisa ser alterado.

4. Como administrador, execute ```mvn clean install```. Executar sem privilégios administrativos impedirá a ferramenta de assinatura de encontrar o certificado.

> [IMPORTANT!]
> Se o pacote MSIX foi assinado com um certificado autoassinado, o certificado também deve ser instalado na máquina de destino.
>
> Consulte [Instalando um certificado autoassinado de um pacote MSIX](#Instalando-um-certificado-autoassinado-de-um-pacote-MSIX).

***

## Etapas opcionais

### Gerando um certificado de assinatura de código autoassinado

O processo de construção requer um certificado de assinatura de código para assinar os executáveis e o pacote MSIX.

Se um certificado de assinatura de código válido não estiver disponível, um certificado autoassinado pode ser criado por meio dos passos abaixo para fins de desenvolvimento.

Execute os seguintes comandos em um terminal do PowerShell:

```
New-SelfSignedCertificate -Type Custom -Subject "CN=THL, O=IPT, C=BR" -KeyUsage DigitalSignature -FriendlyName "THL Certificate" -CertStoreLocation "Cert:\CurrentUser\My" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}")
$password = ConvertTo-SecureString -String "!thl!321#" -Force -AsPlainText
$thumbprint = (Get-ChildItem -Path Cert:\CurrentUser\My | Where-Object {$_.Subject -eq "CN=THL, O=IPT, C=BR"} |Sort-Object LastWriteTime -Descending | Select-Object -first 1).Thumbprint
$user_home = [Environment]::GetFolderPath("UserProfile")
Export-PfxCertificate -Cert "Cert:\CurrentUser\My\$thumbprint" -FilePath "$user_home\THL.pfx" -Password $password
```

Copie o arquivo PFX gerado de `$user_home\THL.pfx` para  `server\jpackage\certificate` na pasta do projeto.

### Instalando o certificado autoassinado incluído

<details open>
<summary><b>Opção 1: Pelo CLI</b></summary>

1. De um prompt do PowerShell elevado, execute o seguinte comando:
   ```
   Import-PfxCertificate -FilePath '.\server\jpackage\certificate\THL.pfx' -CertStoreLocation 'Cert:\LocalMachine\My' -Password (ConvertTo-SecureString '!thl!321#' -AsPlainText -Force)
   ```
</details>

<details open>
<summary><b>Opção 2: Pela GUI</b></summary>

1. Dê um duplo clique no arquivo `server\jpackage\certificate\THL.pfx` para abrir o Assistente de Importação de Certificado.
2. Selecione "Máquina Local" como local do repositório, clique em Avançar.
3. Clique em Avançar.
4. Digite !thl!321# como senha, clique em Avançar.
5. Selecione "Selecionar automaticamente o repositório de certificados com base no tipo de certificado", clique em Avançar.
6. Clique em Concluir.

</details>

### Instalando o certificado autoassinado incluído

Para instalar pacotes MSIX que foram assinados com um certificado autoassinado, é necessário confiar nessa assinatura instalando o certificado no repositório de certificados ```Cert:\LocalMachine\TrustedPeople``` certificate store.

> [Aviso!]
> Instalar um certificado autoassinado no Windows fará com que o sistema confie em todos os pacotes assinados com esse certificado, o que pode incluir alguns maliciosos.
> 
> É altamente recomendável desinstalar o certificado após os testes para garantir a segurança do seu sistema.

<details open>
<summary><b>Opção 1: Pelo CLI</b></summary>

1. Execute o seguinte comando de um prompt do PowerShell elevado. Substitua ```THL-1.0.msix``` pelo caminho para o arquivo MSIX.
   ```
   Get-AuthenticodeSignature 'THL-1.0.msix' | Select-Object -ExpandProperty SignerCertificate | Export-Certificate -FilePath ($TempFile = New-TemporaryFile).FullName; Import-Certificate -FilePath $TempFile.FullName -CertStoreLocation 'Cert:\LocalMachine\TrustedPeople'; Remove-Item -Path $TempFile.FullName
   ```
</details>

<details open>
<summary><b>Opção 2: Pela GUI</b></summary>

1. Abra o menu de contexto do arquivo .msix e abra a janela Propriedades.
2. Na aba Assinaturas Digitais, selecione o certificado THL e clique em Detalhes.
3. Na janela Detalhes da Assinatura Digital, clique em Mostrar Certificado.
4. Na janela do Certificado, clique em Instalar Certificado.
5. No Assistente de Importação de Certificado, selecione "Máquina Local" como local do repositório, clique em Avançar.
6. Selecione "Colocar todos os certificados no seguinte repositório" e clique em Procurar...
7. Selecione o repositório "Pessoas Confiáveis" e clique em OK.
8. Clique em Avançar.
9. Clique em Concluir.

</details>

***

# Documentação de referência

* Win32 Api Docs      (https://learn.microsoft.com/en-us/windows/win32/apiindex/api-index-portal)
* Java 21.x Docs      (https://docs.oracle.com/en/java/javase/21/docs/api/index.html)
* JavaFX 20.x Docs    (https://openjfx.io/javadoc/20/)
* Maven 3.x Docs      (https://maven.apache.org/guides/index.html)
* Git 2.x Docs        (https://git-scm.com/doc)
* Node 20.x Docs      (https://nodejs.org/dist/latest-v20.x/docs/api/)
