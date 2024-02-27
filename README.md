# UbuntuOnActiveDirectory
Este é um guia básico para ingressar o Ubuntu Server no Active Directory do Windows

Para ingressar um servidor Ubuntu no Active Directory do Windows, você pode usar o utilitário realm que vem com o pacote realmd. Aqui estão os passos básicos para realizar essa tarefa:

## 1 . Instale o pacote realmd:

```bash
sudo apt-get update
sudo apt-get install realmd
```

## 2 . Descubra o domínio AD:

### Execute o seguinte comando para descobrir o domínio AD:
No nosso exemplo vamos usar o aliancarebelde.local, já que o nosso servidor é baseado no contexto de entidades de ***Star Wars***
```bash
realm discover aliancarebelde.local
```
Substitua aliancarebelde.local pelo seu domínio AD real. Este comando ajudará a determinar a configuração exata que você precisará usar ao ingressar no domínio.

## 3 . Instale o pacote sssd para integração com o AD:
```bash
sudo apt-get install sssd-tools sssd libnss-sss libpam-sss adcli packagekit
```

## 4 . Ingresse no domínio:
Use o comando realm join para ingressar no domínio AD. Substitua aliancarebelde.local pelo seu domínio AD real:
```bash
sudo realm join -U Administrator aliancarebelde.local
```
Será solicitada a senha do administrador do domínio.

## 5 . Configure o SSSD (System Security Services Daemon):
Após ingressar no domínio, é necessário configurar o SSSD. Abra o arquivo /etc/sssd/sssd.conf em um editor de texto e ajuste conforme necessário. Certifique-se de que as configurações como use_fully_qualified_names e fallback_homedir estejam de acordo com suas preferências.

```bash
sudo nano /etc/sssd/sssd.conf
```
Exemplo de configuração:

```bash
[sssd]
domains = aliancarebelde.local
config_file_version = 2
services = nss, pam

[domain/aliancarebelde.local]
default_shell = /bin/bash
krb5_store_password_if_offline = True
cache_credentials = True
krb5_realm = ALIANCAREBELDE.LOCAL
realmd_tags = manages-system joined-with-adcli
id_provider = ad
fallback_homedir = /home/%u@%d
ad_domain = aliancarebelde.local
use_fully_qualified_names = True
ldap_id_mapping = True
access_provider = ad
```
Lembre-se de substituir aliancarebelde.local pelos detalhes do seu domínio.

## 6 . Reinicie os serviços:
Reinicie os serviços SSSD para aplicar as alterações:
```bash
sudo systemctl restart sssd
```

## 7 . Teste a autenticação:
Você pode testar a autenticação usando o comando id ou tentando fazer login com um usuário do AD:
```bash
id username@aliancarebelde.local
```

Certifique-se de substituir username@aliancarebelde.local pelo seu usuário e domínio reais.

Isso deve configurar o Ubuntu Server para autenticar usuários no Active Directory do Windows. Certifique-se de adaptar as configurações conforme necessário para atender aos requisitos específicos do seu ambiente.