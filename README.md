# Beleza! Para configurar SSL no seu servidor Speedtest na porta 8080, você precisa garantir que:

- O domínio está apontando para o IP do servidor
- A porta 80 está aberta temporariamente para a validação do Let's Encrypt
- O Certbot gera os certificados corretamente
- O Speedtest usa esses certificados

## Instalar o Certbot
```sh
apt update && apt install certbot -y
```

## Para evitar conflitos, pare o Speedtest temporariamente:
```sh
systemctl stop ooklaserver
```

## Agora gere o certificado normalmente:
```sh
certbot certonly --standalone -d speedtest.ncinternet.com.br
```

## Digite 2

## Se tudo der certo, o certificado será salvo em:
```sh
/etc/letsencrypt/live/speedtest.ncinternet.com.br/
```

## Edite o arquivo de configuração do Speedtest:
```sh
nano /usr/local/src/ooklaserver/OoklaServer.properties
```

## Adicione ou edite as seguintes linhas:
```sh
openSSL.server.certificateFile = /etc/letsencrypt/live/speedtest.ncinternet.com.br/fullchain.pem
openSSL.server.privateKeyFile = /etc/letsencrypt/live/speedtest.ncinternet.com.br/privkey.pem
```

## Reinicie o Speedtest
```sh
systemctl restart ooklaserver
```

## Agora, teste acessando:
```sh
https://speedtest.ncinternet.com.br:8080
```

# Configurar Renovação Automática
Como a porta 80 será necessária para renovar, o ideal é usar um webserver para responder à validação automaticamente. Se precisar, me avise que explico como fazer isso com Nginx. 🚀

## Seu Certbot já está configurado para renovar automaticamente o certificado.
Mas como o Speedtest não recarrega os certificados sozinho, crie um script de renovação automática:

## Edite o cron:
```sh
crontab -e
```

## Adicione esta linha:
```sh
0 3 * * * certbot renew --quiet && systemctl restart ooklaserver
```

# ✅ Finalizado!
Agora seu Speedtest roda com HTTPS na porta 8080. 🚀
