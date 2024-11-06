Este repositório contém instruções e scripts para configurar e executar um servidor DHCP usando o Docker. O servidor DHCP (Dynamic Host Configuration Protocol) fornece endereços IP dinamicamente aos dispositivos na rede.
Índice

    O que é DHCP?
    Requisitos
    Como Usar
        Construir a Imagem Docker
        Executar o Servidor DHCP
        Configuração do DHCP
    Comandos Comuns
    Persistência e Reinício Automático
    Problemas Comuns
    Referências

O que é DHCP?

O DHCP (Dynamic Host Configuration Protocol) é um protocolo de rede utilizado para atribuir automaticamente endereços IP a dispositivos em uma rede. Ele simplifica a configuração da rede, permitindo que dispositivos se conectem e recebam um endereço IP sem a necessidade de configuração manual.
Como funciona o DHCP:

    O servidor DHCP aloca endereços IP para os dispositivos clientes na rede.
    Ele também pode fornecer informações adicionais, como o gateway padrão e servidores DNS.

Requisitos

Para rodar o servidor DHCP em Docker, você precisará dos seguintes requisitos:

    Docker instalado no seu sistema.
    Acesso a privileged mode (modo privilegiado) para permitir que o Docker manipule as interfaces de rede.
    Acesso à interface de rede onde o servidor DHCP irá funcionar.

Como Usar

Aqui estão os passos para construir e executar um servidor DHCP usando o Docker.
Construir a Imagem Docker

Este projeto usa a imagem networkboot/dhcpd para executar o servidor DHCP. Se você quiser construir a imagem a partir do código fonte, use o seguinte comando:

bash

docker build -t meu-servidor-dhcp .

Se não quiser construir a imagem, você pode utilizar a imagem oficial diretamente do Docker Hub.
Executar o Servidor DHCP

Depois de ter o Docker configurado, execute o servidor DHCP com o seguinte comando:

bash

docker run -d \
  --name servidor-dhcp \
  --restart unless-stopped \
  --net host \
  --privileged \
  -v /caminho/para/dhcpd.conf:/etc/dhcp/dhcpd.conf \
  networkboot/dhcpd

Explicação dos parâmetros:

    -d: Executa o contêiner em segundo plano.
    --name servidor-dhcp: Nomeia o contêiner como servidor-dhcp.
    --restart unless-stopped: Faz com que o contêiner reinicie automaticamente, exceto se for interrompido manualmente.
    --net host: Usa a rede do host para o contêiner (necessário para manipular interfaces de rede).
    --privileged: Permite que o contêiner tenha acesso total à máquina host, necessário para alterar configurações de rede.
    -v /caminho/para/dhcpd.conf:/etc/dhcp/dhcpd.conf: Monta o arquivo de configuração do DHCP para personalizar o comportamento do servidor.

Configuração do DHCP

O arquivo de configuração padrão do servidor DHCP está localizado em /etc/dhcp/dhcpd.conf. Este arquivo deve ser configurado conforme necessário para o seu ambiente.

Aqui está um exemplo simples de configuração:

bash

# dhcpd.conf
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
  default-lease-time 600;
  max-lease-time 7200;
}

Isso configura o servidor para fornecer endereços IP no intervalo de 192.168.1.100 a 192.168.1.200, com routers e DNS definidos como 192.168.1.1 e 8.8.8.8 (Google DNS).
Comandos Comuns

Aqui estão alguns comandos úteis ao gerenciar seu servidor DHCP no Docker:

    Verificar os logs do contêiner:

    bash

docker logs servidor-dhcp

Verificar o status do contêiner:

bash

docker ps

Parar o servidor DHCP:

bash

docker stop servidor-dhcp

Remover o servidor DHCP:

bash

docker rm servidor-dhcp

Verificar as regras de firewall no Docker:

bash

    sudo iptables -L -n

Persistência e Reinício Automático

Para garantir que o servidor DHCP inicie automaticamente após o reinício do sistema ou do Docker, usamos a opção --restart unless-stopped no comando docker run.

Se precisar salvar o arquivo de configuração do DHCP, certifique-se de montá-lo corretamente com o parâmetro -v para manter as configurações mesmo após a reinicialização do contêiner.
Problemas Comuns

    Problema com permissões de rede: Se o servidor DHCP não estiver funcionando corretamente, pode ser necessário garantir que o Docker tenha permissão para acessar as interfaces de rede do sistema. Use o modo --privileged se necessário.

    Portas em uso: Certifique-se de que as portas necessárias para o servidor DHCP (tipicamente a porta 67/UDP) não estejam bloqueadas ou em uso por outro serviço.

    Arquivo dhcpd.conf não encontrado: Se o contêiner não encontrar o arquivo de configuração, verifique se ele foi montado corretamente no contêiner com o parâmetro -v.
