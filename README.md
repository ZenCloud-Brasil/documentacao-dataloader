# Guia Ubuntu do Cliente: PIS Linux

## O que este serviço faz

Vamos instalar um serviço em segundo plano na sua máquina Ubuntu. Esse serviço recebe comandos do nosso sistema central via HTTPS, lê dados do seu banco Progress e envia os dados extraídos de volta para nós via HTTPS.

O serviço inicia automaticamente quando a máquina liga. Não sendo necessário login ao iniciar.

## Observação importante sobre rede

Este serviço foi planejado para usar apenas HTTPS na comunicação entre a sua maquina e o nosso ambiente.

Isso significa que a sua máquina nao precisa de acesso direto a:

- porta do nosso PostgreSQL
- porta do nosso RabbitMQ
- porta do nosso Redis

O requisito é ter acesso HTTPS de saida para a URL da nossa API https://api-dataloader.manhattan.bi/.

## O que a sua maquina precisa ter

### Sistema operacional

- Ubuntu
- acesso `sudo` para instalacao e reinicio do serviço

### Acesso ao banco de dados

- acesso desta maquina Ubuntu ao banco Progress
- driver ODBC do Progress instalado
- DSN ODBC ou informacoes de conexão configuradas e testadas
- usuário de banco com permissao somente leitura, de preferência

### Acesso de rede

A maquina precisa de acesso de saída para:

- a URL HTTPS da nossa API
- o host do seu banco local/interno

Não e necessário expor nenhuma porta publica para esse serviço, apenas de conexão com a url: https://api-dataloader.manhattan.bi/.

### Pacotes básicos

Vamos precisar de:

- `python3`
- `python3-venv`
- `unixodbc`
- o driver ODBC do fornecedor do Progress

## O que nós vamos instalar

- a aplicacao do listener MIS
- um arquivo seguro de ambiente com as configuracoes de conexão
- um serviço Linux chamado `pis-linux-listener.service`

## O que talvez precisemos que você forneca

- versão do Ubuntu
- hostname da maquina
- pacote do driver ODBC do Progress ou a forma de download
- nome do DSN, ou host/porta/banco/usuario/senha do banco
- regras de firewall ou VPN permitindo HTTPS de saida para a URL da nossa API
- uma pessoa de contato que possa executar comandos com `sudo`, se necessario

## Operação

Normalmente voce não precisa fazer nada.

Nos administramos:

- quais consultas existem
- quando elas rodam
- se a execucao é manual ou agendada

A sua máquina so precisa permanecer ligada, conectada a rede e com acesso ao banco local e ao nosso endpoint HTTPS.

## Comandos que talvez voce precise

### Verificar se o serviço está rodando

```bash
sudo systemctl status pis-linux-listener.service
```

### Reiniciar o serviço

```bash
sudo systemctl restart pis-linux-listener.service
```

### Iniciar o serviço

```bash
sudo systemctl start pis-linux-listener.service
```

### Parar o serviço

```bash
sudo systemctl stop pis-linux-listener.service
```

### Ver logs recentes

```bash
sudo journalctl -u pis-linux-listener.service -n 200 --no-pager
```

### Acompanhar logs em tempo real

```bash
sudo journalctl -u pis-linux-listener.service -f
```

### Se o arquivo do serviço foi alterado

```bash
sudo systemctl daemon-reload
sudo systemctl restart pis-linux-listener.service
```

## Solução simples de problemas

### O serviço esta parado

1. Verifique o status:

```bash
sudo systemctl status pis-linux-listener.service
```

2. Reinicie:

```bash
sudo systemctl restart pis-linux-listener.service
```

3. Se continuar falhando, veja os logs:

```bash
sudo journalctl -u pis-linux-listener.service -n 200 --no-pager
```


## Versão curta

Na maioria dos casos, esses sao os unicos comandos que você vai precisar:

```bash
sudo systemctl status pis-linux-listener.service
sudo systemctl restart pis-linux-listener.service
sudo journalctl -u pis-linux-listener.service -n 200 --no-pager
```
