# Instalação e Configuração do mkcert para Nginx

Este guia explica como instalar e configurar o mkcert para gerar certificados SSL locais válidos para desenvolvimento com Nginx no Debian/Ubuntu.

## Pré-requisitos

- Sistema operacional Debian/Ubuntu
- Acesso de superusuário (sudo)
- Nginx instalado

## 1. Instalação do mkcert

### Método 1: Usando o repositório oficial

```bash
# Atualizar a lista de pacotes
sudo apt update

# Instalar dependências necessárias
sudo apt install libnss3-tools

# Instalar o mkcert a partir do repositório oficial
sudo apt install mkcert
```

### Método 2: Baixar o binário pré-compilado e movê-lo para um diretório PATH

```bash
# 1. Defina a variável de versão (EXEMPLO: Verifique a versão mais recente!)
export MKCERT_VERSION="v1.4.4"

# 2. Baixe o binário para Linux (arquitetura amd64)
wget [https://github.com/FiloSottile/mkcert/releases/download/$](https://github.com/FiloSottile/mkcert/releases/download/$){MKCERT_VERSION}/mkcert-${MKCERT_VERSION}-linux-amd64

# 3. Conceda permissão de execução
chmod +x mkcert-${MKCERT_VERSION}-linux-amd64

# 4. Mova o arquivo para um diretório no PATH e renomeie
sudo mv mkcert-${MKCERT_VERSION}-linux-amd64 /usr/local/bin/mkcert
```
## 2. Instalação da Autoridade Certificadora (CA) Local

```bash
# Instalar a CA local no sistema
mkcert -install

# Verificar se a CA foi instalada corretamente
mkcert -CAROOT
```
A CA será instalada em `~/.local/share/mkcert`

## 3. Gerar os Certificados SSL
```bash
# Criar diretório para os certificados
sudo mkdir -p /etc/nginx/ssl

# Gerar certificado para localhost
mkcert nomecertificado.local localhost
```
> **Importante**: Gere os certificados sem `sudo` e fora do diretório `/etc/nginx/ssl`, e posteriormente os mova com `sudo mv nomecertificado.local+1-key.pem nomecertificado.local+1.pem /etc/nginx/ssl/`

## 4. Configuração do Nginx
Crie ou edite o arquivo de configuração do seu site:

```bash
# Utilizado o editor de texto nano como exemplo
sudo nano /etc/nginx/sites-available/default
```
Adicione a seguinte configuração:

```bash
server {
	listen 443 ssl;
	server_name localhost;

	ssl_certificate /etc/nginx/ssl/gcsi.local+1.pem;
	ssl_certificate_key /etc/nginx/ssl/gcsi.local+1-key.pem;

	root caminhoDoProjeto;
	
	index index.html index.htm index.nginx-debian.html;

	location / {
		try_files $uri $uri/ /index.html;
	}
}
```

## 5. Testar a configuração e fazer ajustes finais
#### Testar configuração
```bash
sudo nginx -t
```
#### Adicionar a regra https do Nginx no firewall
```bash
sudo ufw allow "Nginx HTTPS"
```
#### Reiniciar o firewall
```bash
sudo ufw reload
```
#### Reiniciar o Nginx
```bash
sudo systemctl restart nginx
```

## 6. Verificar o projeto no navegador
- Acesse https://localhost ou https://meusite.local
- O certificado deve aparecer como válido e seguro
- Clique no cadeado na barra de endereços para ver os detalhes do certificado
