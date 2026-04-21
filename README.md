# Portainer

Stack do Portainer CE para Docker Swarm, com agente distribuido e publicacao via Traefik.

## O que sobe

- `agent`: executa em modo `global` para expor informacoes do cluster ao Portainer.
- `portainer`: interface web do Portainer, executada apenas em node manager.

## Pre-requisitos

- Docker Swarm inicializado.
- Rede overlay `traefik-public` existente.
- Traefik funcional no cluster, com o resolver `cloudflare` configurado.
- Arquivo [`.env`](./.env) com o host desejado.

Exemplo:

```env
PORTAINER_HOST=portainer.seu-dominio.com
```

Se precisar criar o arquivo:

```bash
cp .env.example .env
```

## Deploy

No contexto deste repositorio, o deploy correto e:

```bash
make deploy-portainer
```

Esse alvo agora carrega automaticamente `portainer/.env` antes de executar o `docker stack deploy`.

Se quiser executar manualmente:

```bash
set -a && source .env && set +a
docker stack deploy -c docker-compose.yml portainer
```

## Exposicao

- Traefik publica o Portainer pelo arquivo dinamico [`traefik/infra/traefik/dynamic/portainer.yml`](../traefik/infra/traefik/dynamic/portainer.yml).
- As labels no [`docker-compose.yml`](./docker-compose.yml) ficam comentadas como referencia.
- A porta `9000` permanece publicada no host para acesso direto/local e bootstrap inicial, se necessario.
- O mesmo arquivo dinamico tambem contem a rota `/outpost.goauthentik.io/` do Authentik.

## Authentik

O middleware `authentik-portainer-middleware@file` continua definido no Traefik, mas nao esta aplicado por padrao ao router principal do Portainer.

Se quiser proteger o Portainer com Authentik, habilite essa middleware no router `portainer` dentro do arquivo dinamico.

## Persistencia

- `portainer_data`: dados persistentes da aplicacao.

## Redes

- `agent_network`: comunicacao entre o Portainer e os agents.
- `traefik-public`: exposicao via Traefik.
