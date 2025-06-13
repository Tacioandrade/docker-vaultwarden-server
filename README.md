# Vaultwarden em docker-compose.yml para ser rodado com algum proxy reverso

Esse meu projeto é um fork de outro aqui do Github, porém enquanto o intuito do projeto original
é ser rodado em um servidor apenas com o [Vaultwarden](https://github.com/dani-garcia/vaultwarden)
em seu servidor, esse meu projeto nada mais é que uma pequena modificação dele que roda o Vaultwarden
na porta 280 do seu host e que deve usar algum proxy reverso a sua escolha como:
- Apache2 - Possui um modelo de arquivo de configuração no repositório
- nginx - Possui um modelo de arquivo de configuração no repositório
- NginxProxyManager
- Qualquer outro proxy reverso do mercado

## Prerequisitos

Antes de começar a mexer com o projeto, tenha os seguintes itens prontos
- Git
- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- Um domínio ou subdomínio configurado e apontando para o servidor onde está o seu Vaultwarden


## Getting Started

Clone esse repositório para seu servidor
```shell
git clone https://github.com/tacioandrade/docker-vaultwarden-server/
```

Entre no repositório clonado
```shell
cd docker-vaultwarden-server/
```

Copie o arquivo `.env.example` para o arquivo de variáveis `.env`
```shell
cp .env.example .env
```

Edire o arquivo `.env`, modificando as variáveis que achar necessário, inicialmente só é necessário a VIRTUAL_HOST
```shell
nano .env
```

Por fim, suba o projeto com o comando:
```shell
docker compose up -d
```

## Configurações

Após subir o Vaultwarden, configure o proxy reverso que desejar para este projeto e use a ferramenta certbot para
gerar o certificado https válido para seu subdomínio que está usando para o Vaultwarden apontando para http://ip-host:280

Com o sistema abrindo pelo domínio correto, recomendo que entre na página de administração do Vualtwaden e faça a configuração
do mesmo. a página será https://senhas.seudominio.com/admin e a senha é admin123

Após configurar as opções corretamente, conforme seu uso, recomendo trocar essa senha, para evitar que outras pessoas tenham
acesso a página de configuração do sistema.

Além disso, caso queira aumentar a segurança, edite o .env e troque de false para true a opção: DISABLE_ADMIN_TOKEN, para que
com isso a página de administração fique desativada.

Após toda essa configuração ser feita, pare o Vaultwarden e suba novamente para carregar as novas configurações:
```shell
docker compose down
docker compose up -d
```

## Segue abaixo uma explicação das variáveis de ambiente do .env para que consiga entender melhor e implementar da melhor forma possível

### SIGNUPS_ALLOWED

By default, anyone who can access your instance can register for a new account. To disable this, set the
`SIGNUPS_ALLOWED` env variable to false.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Disable-registration-of-new-users)

### SIGNUPS_DOMAINS_WHITELIST

You can restrict registration to email addresses from certain domains by setting `SIGNUPS_DOMAINS_WHITELIST` accordingly.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Disable-registration-of-new-users#restricting-registrations-to-certain-email-domains)

### SIGNUPS_VERIFY

Require email verification to finish the registration.

### REQUIRE_DEVICE_EMAIL

Require new device emails. When a user logs in an email is required to be sent.

### INVITATIONS_ALLOWED

Even when registration is disabled (`SIGNUPS_ALLOWED`), organization administrators or owners can invite users to join
organization.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Disable-invitations)

### ADMIN_TOKEN

Activated the admin page. This page allows server administrators to view all the registered users and to delete them. It
also shows inviting
new users, even when registration is disabled.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-admin-page)

[Secure the ADMIN_TOKEN](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-admin-page#secure-the-admin_token)

### DISABLE_ADMIN_TOKEN

If you have another method to authenticate the admin page then you can set the `DISABLE_ADMIN_TOKEN` variable to true.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Disable-admin-token)

### DOMAIN

The domain of your vaultwarden instance (should be the same as `VIRTUAL_HOST`).

This is required for U2F and FIDO2 WebAuthn authentication.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-U2F-%28and-FIDO2-WebAuthn%29-authentication)

### YubiKey OTP Authentication

You need a `YUBICO_CLIENT_ID` and `YUBICO_SECRET_KEY` to allow authentication with a Yubikey.

If `YUBICO_SERVER` is not set the default YubiCloud servers are used.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-Yubikey-OTP-authentication)

### SMTP Configuration

- `SMTP_HOST`: The host server of the mail server
- `SMTP_FROM`: the mail address which should be used for sending mails
- `SMTP_FROM_NAME`: the name which should be used for sending mails
- `SMTP_PORT`: the port of the smtp server
- `SMTP_SECURITY`: the protocol that should be used (default: starttls, options: force_tls, off, starttls)
- `SMTP_USERNAME`: the username of the smtp user
- `SMTP_PASSWORD`: the password of the smtp user
- `SMTP_EMBED_IMAGES`: embed images as email attachments

This requires to set the `DOMAIN` variable.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/SMTP-Configuration)

### SHOW_PASSWORD_HINT

Usually, password hints are sent by email. But as vaultwarden is made with small or personal deployment in mind,
hints are also available from the password hint page, so you don't have to configure an email service.

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Password-hint-display)

### EMAIL_CHANGE_ALLOWED

Controls whether users can change their email. This setting applies globally to all users.


### Logging

- `LOG_LEVEL`: options are: "trace", "debug", "info", "warn", "error" or "off". NOTE: Using the log level "warn" or "error" still allows Fail2Ban to work properly.
- `USE_SYSLOG`
- `EXTENDED_LOGGING`

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Logging)

### Syncing users from LDAP

[More information](https://github.com/dani-garcia/vaultwarden/wiki/Syncing-users-from-LDAP)

### Push Notifications

To enable the push notifications you have to get a [Hosting Installation id and Key from here](https://bitwarden.com/host/).

```text
PUSH_ENABLED=true
PUSH_INSTALLATION_ID=CHANGEME
PUSH_INSTALLATION_KEY=CHANGEME
```

To use "European Data" Region set these variables (default):

```text
PUSH_RELAY_URI=https://push.bitwarden.eu
PUSH_IDENTITY_URI=https://identity.bitwarden.eu
```

if you want to use the "US Data" region use these instead:

```text
PUSH_RELAY_URI=https://push.bitwarden.com
PUSH_IDENTITY_URI=https://identity.bitwarden.com
```

### SENDS_ALLOWED

Controls whether users are allowed to create Bitwarden Sends.

### EMERGENCY_ACCESS_ALLOWED

Controls whether users can enable emergency access to their accounts.
