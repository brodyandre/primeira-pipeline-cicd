# Checklist de Screenshots

Este guia organiza as capturas de tela mais úteis para documentar a execução do laboratório.

## Ordem sugerida

1. Home do repositório com o README
2. Lista de execuções do GitHub Actions
3. Execução detalhada do workflow com `CI` e `CD` verdes
4. Tags da imagem no Docker Hub
5. Estado do deployment no Kubernetes
6. Aplicação respondendo no navegador
7. Resposta HTTP em terminal
8. Status do self-hosted runner

## Pasta de destino

```bash
mkdir -p assets/screenshots
```

## Captura 1

Nome sugerido:

```text
01-repo-home-readme-hero.png
```

O que mostrar:

- nome do repositório
- badges
- início do README

Como abrir:

```bash
explorer.exe "https://github.com/brodyandre/primeira-pipeline-cicd"
```

## Captura 2

Nome sugerido:

```text
02-actions-run-list-main-success.png
```

O que mostrar:

- página do GitHub Actions
- último run bem-sucedido da branch `main`

Comandos:

```bash
gh run list -R brodyandre/primeira-pipeline-cicd --limit 5
explorer.exe "https://github.com/brodyandre/primeira-pipeline-cicd/actions"
```

## Captura 3

Nome sugerido:

```text
03-actions-workflow-jobs-success.png
```

O que mostrar:

- detalhes do workflow
- job de `CI` verde
- job de `CD` verde

Comando:

```bash
explorer.exe "https://github.com/brodyandre/primeira-pipeline-cicd/actions/runs/26840743902"
```

## Captura 4

Nome sugerido:

```text
04-dockerhub-image-tags.png
```

O que mostrar:

- repositório `las43/primeira-pipeline-cicd`
- tags recentes

Abrir no navegador:

```text
https://hub.docker.com/r/las43/primeira-pipeline-cicd/tags
```

## Captura 5

Nome sugerido:

```text
05-kubernetes-deployment-status-terminal.png
```

O que mostrar:

- `deployment`
- `pods`
- `service`
- imagem em execução

Comandos:

```bash
kubectl get deployment web -o wide
kubectl get pods -l app=web -o wide
kubectl get svc web -o wide
kubectl get deployment web -o jsonpath='{.spec.template.spec.containers[0].image}' && echo
```

## Captura 6

Nome sugerido:

```text
06-application-browser-response.png
```

O que mostrar:

- navegador acessando a aplicação
- resposta visível da aplicação

Comando:

```bash
kubectl port-forward svc/web 18080:80
```

Depois abrir:

```text
http://127.0.0.1:18080
```

## Captura 7

Nome sugerido:

```text
07-application-http-200-terminal.png
```

O que mostrar:

- `HTTP/1.1 200 OK`
- `Content-Type: text/plain`
- `Aplicacao exemplo`

Comando:

```bash
curl -i http://127.0.0.1:18080
```

## Captura 8

Nome sugerido:

```text
08-self-hosted-runner-service-status.png
```

O que mostrar:

- serviço do runner ativo
- nome do serviço
- status `active (running)`

Comandos:

```bash
cd ~/actions-runner-primeira-pipeline-cicd
sudo ./svc.sh status
```

## Dicas visuais

- use nomes com prefixo numérico para manter a ordem
- prefira navegador e terminal em tela limpa
- corte áreas vazias antes de subir as imagens
- mantenha fontes legíveis
- não exponha tokens, secrets ou informações sensíveis
