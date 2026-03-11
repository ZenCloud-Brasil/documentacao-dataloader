# Guia Ubuntu do Cliente: MIS Data Listener

## O que este servico faz

Vamos instalar um servico em segundo plano na sua maquina Ubuntu. Esse servico recebe comandos do nosso sistema central via HTTPS, le dados do seu banco Progress local e envia os dados extraidos de volta para nos via HTTPS.

O servico inicia automaticamente quando a maquina liga. Ninguem precisa fazer login para ele funcionar.

## Observacao importante sobre rede

Este servico foi planejado para usar apenas HTTPS na comunicacao entre a sua maquina e o nosso ambiente.

Isso significa que a sua maquina nao precisa de acesso direto a:

- porta do nosso PostgreSQL
- porta do nosso RabbitMQ
- porta do nosso Redis

O requisito normal e ter acesso HTTPS de saida para a URL da nossa API.

## O que a sua maquina precisa ter

### Sistema operacional

- Ubuntu 22.04 LTS ou 24.04 LTS recomendado
- acesso `sudo` para instalacao e reinicio do servico

### Acesso ao banco de dados

- acesso desta maquina Ubuntu ao banco Progress/OpenEdge local
- driver ODBC do Progress/OpenEdge instalado
- DSN ODBC ou informacoes de conexao configuradas e testadas
- usuario de banco com permissao somente leitura, de preferencia

### Acesso de rede

A maquina precisa de acesso de saida para:

- a URL HTTPS da nossa API
- o host do seu banco local/interno

Nao e necessario expor nenhuma porta publica para esse servico.

### Pacotes basicos

Normalmente vamos precisar de:

- `python3`
- `python3-venv`
- `unixodbc`
- o driver ODBC do fornecedor do Progress/OpenEdge

## O que nos vamos instalar

- a aplicacao do listener MIS
- um arquivo seguro de ambiente com as configuracoes de conexao
- um servico Linux chamado `mis-dataloader-listener.service`

## O que talvez precisemos que voce forneca

- versao do Ubuntu
- hostname da maquina
- pacote do driver ODBC do Progress/OpenEdge ou a forma de download
- nome do DSN, ou host/porta/banco/usuario/senha do banco
- regras de firewall ou VPN permitindo HTTPS de saida para a URL da nossa API
- uma pessoa de contato que possa executar comandos com `sudo`, se necessario

## Operacao normal

Normalmente voce nao precisa fazer nada.

Nos administramos:

- quais consultas existem
- quando elas rodam
- se a execucao e manual ou agendada

A sua maquina so precisa permanecer ligada, conectada a rede e com acesso ao banco local e ao nosso endpoint HTTPS.

## Comandos que talvez voce precise

### Verificar se o servico esta rodando

```bash
sudo systemctl status mis-dataloader-listener.service
```

### Reiniciar o servico

```bash
sudo systemctl restart mis-dataloader-listener.service
```

### Iniciar o servico

```bash
sudo systemctl start mis-dataloader-listener.service
```

### Parar o servico

```bash
sudo systemctl stop mis-dataloader-listener.service
```

### Ver logs recentes

```bash
sudo journalctl -u mis-dataloader-listener.service -n 200 --no-pager
```

### Acompanhar logs em tempo real

```bash
sudo journalctl -u mis-dataloader-listener.service -f
```

### Se o arquivo do servico foi alterado

```bash
sudo systemctl daemon-reload
sudo systemctl restart mis-dataloader-listener.service
```

## Solucao simples de problemas

### O servico esta parado

1. Verifique o status:

```bash
sudo systemctl status mis-dataloader-listener.service
```

2. Reinicie:

```bash
sudo systemctl restart mis-dataloader-listener.service
```

3. Se continuar falhando, veja os logs:

```bash
sudo journalctl -u mis-dataloader-listener.service -n 200 --no-pager
```

### Erro de ODBC ou banco de dados

Causas comuns:

- driver ODBC nao instalado
- nome do DSN incorreto
- servidor de banco inacessivel
- usuario ou senha alterados

Verificacoes uteis:

```bash
odbcinst -j
```

Se houver um DSN configurado e o `isql` estiver disponivel:

```bash
isql -v NOME_DO_SEU_DSN
```

### Erro de conectividade HTTPS

Causas comuns:

- VPN desconectada
- regra de firewall de saida ausente
- restricao de proxy
- problema de resolucao DNS
- indisponibilidade temporaria do nosso endpoint HTTPS

Se necessario, um teste simples de conectividade e:

```bash
curl -I https://SEU-ENDPOINT-MIS
```

Se o servico mostrar falhas repetidas de upload ou de polling, reinicie-o depois que a conectividade for restaurada.

## Quando entrar em contato conosco

Entre em contato conosco se:

- o servico continuar parando depois do reinicio
- o driver ODBC for atualizado ou removido
- a senha do banco mudar
- o hostname ou IP do servidor mudar
- o acesso HTTPS de saida para a nossa API mudar
- a maquina for substituida ou reinstalada
- os resultados das consultas parecerem incompletos ou duplicados

## Versao curta

Na maioria dos casos, estes sao os unicos comandos que voce vai precisar:

```bash
sudo systemctl status mis-dataloader-listener.service
sudo systemctl restart mis-dataloader-listener.service
sudo journalctl -u mis-dataloader-listener.service -n 200 --no-pager
```
