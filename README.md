# Squid Proxy Server

# Instalação


## Ubuntu:

Instalando e configurando o Squid no Ubuntu

### Atualização dos repositórios:

Antes de instalar o Squid, é uma boa prática atualizar a lista de pacotes do sistema. No terminal, execute:

```bash
sudo apt update
```
### Instalação do Squid:

Após a atualização, você pode instalar o Squid digitando o seguinte comando no terminal:

```bash
sudo apt install squid
```
### Configuração do Squid:

Depois da instalação, o Squid já estará rodando como um serviço. O arquivo de configuração principal do Squid é /etc/squid/squid.conf. Você pode editá-lo conforme necessário. Por exemplo:

```bash
sudo nano /etc/squid/squid.conf
```
Algumas configurações comuns incluem definir as portas, regras de ACL (listas de controle de acesso), configurações de cache, entre outros. Abaixo um arquivo de exemplo

```plane
# Configurações de porta
http_port 3128

# ACLs (Listas de Controle de Acesso)
acl localnet src 192.168.0.0/24       # ACL para permitir conexões da rede local
acl localnet src 10.0.0.0/8           # Adicione ACLs para outras redes locais, se necessário
acl SSL_ports port 443                # ACL para portas SSL

# Definição de regras de acesso
http_access allow localnet            # Permitir acesso à rede local
http_access deny all                  # Negar acesso a todas as outras fontes

# Definições de cache
cache_mem 512 MB                      # Memória alocada para cache
maximum_object_size 100 MB            # Tamanho máximo de objeto em cache
cache_dir ufs /var/spool/squid 100 16 256  # Diretório de cache

# Log de acessos
access_log /var/log/squid/access.log

# Outras configurações adicionais podem ser adicionadas conforme necessário

```

### Reinicialização do Squid:

Após fazer as alterações no arquivo de configuração, reinicie o serviço do Squid para aplicar as configurações:

```bash
sudo systemctl restart squid
```

### Verificação do status do Squid:

Você pode verificar o status do Squid para garantir que esteja em execução corretamente:

```bash
sudo systemctl status squid
```

## Docker

### Instalação do Docker Compose:

Certifique-se de que o Docker Compose esteja instalado em sua máquina Ubuntu. Você pode seguir as instruções de instalação no site oficial do Docker: https://docs.docker.com/compose/install/.

### Instalação do Docker (caso não esteja instalado):

Se o Docker ainda não estiver instalado em seu sistema Ubuntu, você pode instalar executando os seguintes comandos no terminal:

```bash
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

### Criando o arquivo docker-compose.yaml:

Crie um diretório para o seu projeto Squid e, dentro dele, crie um arquivo chamado docker-compose.yaml. Abra esse arquivo com seu editor de texto preferido.

### Definindo o serviço Squid no Docker Compose:

No arquivo docker-compose.yaml, adicione o seguinte conteúdo para definir o serviço Squid juntamente com a instalação do Docker no Ubuntu:

```yaml
version: '3.8'

services:
  squid:
    image: sameersbn/squid:latest
    container_name: squid
    restart: always
    ports:
      - "3128:3128"
    volumes:
      # Arquivo de configuração do Squid
      - ./squid.conf:/etc/squid/squid.conf
      # Lista de sites bloqueados
      - ./bloqueados:/etc/squid/bloqueados.txt
      # Arquivo de log
      - ./logs:/var/log/squid
      # Diretório de cache
      - ./cache:/var/spool/squid
```

### Configuração do Squid:

Depois da instalação, o Squid já estará rodando como um serviço. O arquivo de configuração principal do Squid é /etc/squid/squid.conf. Você pode editá-lo conforme necessário. Por exemplo:

```bash
sudo nano /etc/squid/squid.conf
```
Algumas configurações comuns incluem definir as portas, regras de ACL (listas de controle de acesso), configurações de cache, entre outros. Abaixo um arquivo de exemplo

```plane
# Configurações de porta
http_port 3128

# ACLs (Listas de Controle de Acesso)
acl localnet src 192.168.0.0/24       # ACL para permitir conexões da rede local
acl localnet src 10.0.0.0/8           # Adicione ACLs para outras redes locais, se necessário
acl SSL_ports port 443                # ACL para portas SSL

# Definição de regras de acesso
http_access allow localnet            # Permitir acesso à rede local
http_access deny all                  # Negar acesso a todas as outras fontes

# Definições de cache
cache_mem 512 MB                      # Memória alocada para cache
maximum_object_size 100 MB            # Tamanho máximo de objeto em cache
cache_dir ufs /var/spool/squid 100 16 256  # Diretório de cache

# Log de acessos
access_log /var/log/squid/access.log

# Outras configurações adicionais podem ser adicionadas conforme necessário

```


### Iniciando o contêiner Squid e o contêiner Ubuntu:
No mesmo diretório onde está o arquivo docker-compose.yaml, execute o seguinte comando para iniciar o serviço Squid e o contêiner Ubuntu:

```bash
docker-compose up -d
```


# Configuração do firewall (opcional):

Se estiver executando um firewall em sua máquina, você precisará permitir o tráfego na porta do Squid (padrão 3128) para que outros dispositivos possam se conectar ao proxy. 

# Testando o Squid:
Após a configuração, você pode testar o Squid configurando um navegador da web ou outra aplicação para usar o Squid como um proxy HTTP na porta configurada (por padrão, 3128). 

## No Firefox

### Abra as Configurações do Firefox

Abra o Firefox em seu computador.
Clique no menu no canto superior direito (três linhas horizontais) e selecione "Preferências" (em sistemas macOS, isso pode ser "Preferências do Firefox").

### Acesse as Configurações de Rede

No painel esquerdo das preferências, selecione "Geral" e role para baixo até encontrar a seção "Rede e Conexão".
Clique em "Configurações..." ao lado de "Conexão".

### Configure o Proxy

Selecione a opção "Configuração manual de proxy".
Na seção "HTTP Proxy", insira "localhost" na caixa de texto ao lado e "3128" na caixa de porta.
Se desejar, você também pode configurar proxies para HTTPS, FTP e SOCKS. Se o seu servidor proxy local suportar esses protocolos e estiver rodando nas mesmas configurações de porta, você pode configurá-los da mesma maneira.
Marque a caixa "Usar este proxy para todos os protocolos".
Clique em "OK" para salvar as configurações.

### Verificar a Conexão

Após salvar as configurações, feche a janela de preferências.
