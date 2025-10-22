# 🧰 Tutorial de Instalação e Configuração do mkcert com Nginx (Ubuntu)

Este guia descreve o passo a passo para instalar e configurar o **mkcert** no Ubuntu, gerar certificados SSL locais e configurar o **Nginx** para servir seu site com HTTPS.

---

## 1. Atualizar os pacotes do sistema

```bash
sudo apt update 
```
## 2. Instalar dependências necessárias
```bash
sudo apt install libnss3-tools -y
```
## 3. Instalar o mkcert
```bash
sudo apt install mkcert -y
mkcert --version # Verificar se está instalado
```
## 4.Criar a autoridade certificadora local (CA)
```bash
mkcert -install
```
## 5. Gerar certificado SSL
```bash
mkcert gcsi local localhost 127.0.0.1 ::1

Criação de certificado valido para os seguintes nomes 
 - "gcsi"
 - "local"
 - "localhost"
 - "127.0.0.1"
 - "::1"

O certificado está em "./gcsi+4.pem" e a chave em "./gcsi+4-key.pem" 
```
## 6. Organização dos certificados
```bash
cd /etc/nginx
sudo mkdir ssl
sudo mv ~/gcsi+4.pem ~/gcsi+4-key.pem /etc/nginx/ssl/
```
Mudança na configuração de `leitura, escrita e execução` dos arquivos:

```bash
sudo chmod 600 /etc/nginx/ssl/gcsi+4-key.pem
sudo chmod 644 /etc/nginx/ssl/gcsi+4.pem
```
## 7. Configurar Nginx para HTTPS
```bash
cd /etc/nginx/sites-available
sudo nano default
```
Substitua o conteúdo do arquivo `default` pelo exemplo abaixo:

```nginx
# --- HTTPS ---
server {
    listen 443 ssl;
    
    root /var/www/html;
    index index.html index.htm;
    
    server_name localhost;

    ssl_certificate     /etc/nginx/ssl/gcsi+4.pem;
    ssl_certificate_key /etc/nginx/ssl/gcsi+4-key.pem;

    location / {
        try_files $uri $uri/ =404;
    }
}

# --- HTTP (redireciona para HTTPS) ---
server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    return 301 https://$host$request_uri;
}
```
## 8. Atualizar /etc/hosts
```bash
sudo nano /etc/hosts
# colar o código abaixo
127.0.0.1 localhost
```

## 9. Testar e reiniciar o Nginx
```bash
sudo nginx -t
sudo systemctl restart nginx
```
## 10. Testar no navegador
```bash
https://localhost
```



