<a id="topo"></a>

<div align="center">

# 🚀 Primeira Pipeline CI/CD

<p>
  <img src="https://img.shields.io/github/actions/workflow/status/brodyandre/primeira-pipeline-cicd/main.yml?branch=main&style=for-the-badge&label=CI%2FCD" alt="Status do workflow">
  <img src="https://img.shields.io/badge/Go-1.22-00ADD8?style=for-the-badge&logo=go" alt="Go 1.22">
  <img src="https://img.shields.io/badge/Docker-Multi--stage-2496ED?style=for-the-badge&logo=docker" alt="Docker">
  <img src="https://img.shields.io/badge/Kubernetes-k3d-326CE5?style=for-the-badge&logo=kubernetes" alt="Kubernetes k3d">
  <img src="https://img.shields.io/badge/Runner-self--hosted-success?style=for-the-badge&logo=githubactions" alt="Self-hosted runner">
</p>

<p>
  <strong>Pipeline CI/CD com Go, Docker, GitHub Actions, self-hosted runner e Kubernetes local.</strong>
</p>

<p>
  Este repositório reúne uma aplicação simples em Go com build containerizado, publicação no Docker Hub,
  deploy automatizado em Kubernetes local e validação operacional do fluxo.
</p>

</div>

> [!IMPORTANT]
> Este projeto cobre o ciclo completo entre código-fonte, build de imagem, publicação em registry,
> execução do deploy e validação de rollout em Kubernetes local.

<a id="indice"></a>

## 📚 Índice

Leitura sugerida: visão geral do projeto, maturidade da solução, arquitetura, pipeline e validação operacional.

- [🎯 Visão Geral](#visao-geral)
- [✨ O que este projeto demonstra](#o-que-este-projeto-demonstra)
- [🥉🥈🥇 Níveis de maturidade: Bronze, Silver e Gold](#niveis-de-maturidade-da-solucao)
- [🏗️ Arquitetura e fluxo](#arquitetura-e-fluxo)
- [🧰 Stack utilizada](#stack-utilizada)
- [📁 Estrutura do repositório](#estrutura-do-repositorio)
- [⚙️ Aplicação Go](#aplicacao-go)
- [🐳 Build da imagem Docker](#build-da-imagem-docker)
- [☸️ Deploy no Kubernetes](#deploy-no-kubernetes)
- [🔄 Pipeline CI/CD](#pipeline-cicd)
- [🧪 Teste manual da aplicação](#teste-manual-da-aplicacao)
- [🛠️ Troubleshooting](#troubleshooting)
- [✅ Evidências de validação](#evidencias-de-validacao)
- [🧭 Decisões técnicas do projeto](#decisoes-tecnicas-do-projeto)
- [🚀 Como executar localmente](#como-executar-localmente)
- [📈 Próximas evoluções](#proximas-evolucoes)

<a id="visao-geral"></a>

## 🎯 Visão Geral

Este projeto entrega uma aplicação HTTP simples em Go, empacotada em uma imagem Docker e publicada automaticamente no Docker Hub.
Depois do build, o pipeline executa o deploy em um cluster Kubernetes local via `kubectl`, usando um runner self-hosted configurado
na própria máquina que hospeda o cluster.

Na prática, este repositório mostra:

- pipeline completa de `CI` e `CD`
- build de imagem com Docker multi-stage
- autenticação segura com `secrets` e `variables`
- deploy automatizado em cluster local
- validação de rollout no Kubernetes
- troubleshooting real de integração entre GitHub Actions, Docker Hub e runner self-hosted

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="o-que-este-projeto-demonstra"></a>

## ✨ O que este projeto demonstra

- **Automação ponta a ponta**: do `push` na `main` até a atualização da imagem em execução no cluster.
- **Conhecimento de containers**: build de imagem otimizada com Docker multi-stage.
- **Operação de CI/CD realista**: separação entre job de `CI` e job de `CD`.
- **Uso correto de credenciais**: integração com Docker Hub via `DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN`.
- **Execução híbrida**: `CI` em `ubuntu-latest` e `CD` em runner self-hosted com acesso ao cluster local.
- **Kubernetes na prática**: `Deployment`, `Service`, rollout e verificação pós-deploy.
- **Capacidade de debugging**: correção de falhas de autenticação e de orquestração do runner.

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="niveis-de-maturidade-da-solucao"></a>

## 🥉🥈🥇 Níveis de maturidade: Bronze, Silver e Gold

Uma forma simples de interpretar a evolução deste projeto é por camadas de maturidade:

- **Bronze**: aplicação funcional, versionada, com `Dockerfile` e execução local previsível.
- **Silver**: automação de `CI/CD`, publicação da imagem em registry, uso de `secrets` e deploy automatizado no cluster.
- **Gold**: operação mais robusta, com observabilidade, testes adicionais, políticas de segurança, rollback mais sofisticado e controles de qualidade mais fortes.

Hoje, este repositório já cobre bem a camada **Silver** e começa a se aproximar de uma camada **Gold** ao incluir deploy automatizado em Kubernetes local, validação de rollout e troubleshooting documentado. As evoluções restantes estão mapeadas na seção de melhorias futuras.

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="arquitetura-e-fluxo"></a>

## 🏗️ Arquitetura e fluxo

```mermaid
flowchart LR
    A[Push ou merge na main] --> B[GitHub Actions - CI]
    B --> C[Build da imagem Docker]
    C --> D[Push para Docker Hub]
    D --> E[GitHub Actions - CD]
    E --> F[Runner self-hosted local]
    F --> G[kubectl apply]
    G --> H[Cluster Kubernetes local - k3d]
    H --> I[Deployment web]
    I --> J[Service web]
    J --> K[Teste manual via port-forward]
```

### Fluxo resumido

1. O código é versionado no GitHub.
2. O workflow em [`.github/workflows/main.yml`](.github/workflows/main.yml) inicia a etapa de `CI`.
3. A aplicação é empacotada em imagem Docker e enviada ao Docker Hub com tag versionada.
4. O job de `CD` roda em um runner self-hosted com acesso ao cluster local.
5. O manifesto Kubernetes é atualizado com a nova tag da imagem.
6. O deploy é aplicado com `kubectl`.
7. O rollout é validado automaticamente ao final do pipeline.

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="stack-utilizada"></a>

## 🧰 Stack utilizada

| Camada | Tecnologia | Objetivo |
| --- | --- | --- |
| Aplicação | Go 1.22 | Servir uma resposta HTTP simples |
| Containerização | Docker | Build e empacotamento da aplicação |
| Registro de imagens | Docker Hub | Armazenar imagens versionadas |
| CI/CD | GitHub Actions | Automatizar build, push e deploy |
| Execução do deploy | Self-hosted runner | Rodar o `kubectl` próximo ao cluster |
| Orquestração | Kubernetes | Executar a aplicação em cluster |
| Cluster local | k3d | Ambiente local leve para demonstração |

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="estrutura-do-repositorio"></a>

## 📁 Estrutura do repositório

| Caminho | Finalidade |
| --- | --- |
| [`src/main.go`](src/main.go) | Código-fonte da aplicação Go |
| [`src/Dockerfile`](src/Dockerfile) | Build multi-stage da imagem |
| [`k8s/deployment.yaml`](k8s/deployment.yaml) | `Deployment` e `Service` da aplicação |
| [`.github/workflows/main.yml`](.github/workflows/main.yml) | Pipeline de CI/CD |

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="aplicacao-go"></a>

## ⚙️ Aplicação Go

A aplicação é propositalmente simples para deixar o foco do projeto na esteira de entrega. Ela sobe um servidor HTTP na porta `5000`
e responde texto puro no endpoint raiz:

```txt
Aplicacao exemplo
```

Esse recorte deixa o foco do repositório na esteira de entrega, no build da imagem,
no deploy automatizado e na operação do ambiente local.

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="build-da-imagem-docker"></a>

## 🐳 Build da imagem Docker

O arquivo [`src/Dockerfile`](src/Dockerfile) usa **multi-stage build**:

- a primeira etapa compila a aplicação em Go
- a segunda etapa cria uma imagem final mais enxuta
- a execução acontece com um usuário não-root (`appuser`)

Esse desenho ajuda a demonstrar preocupação com:

- tamanho da imagem
- separação entre build e runtime
- segurança básica de execução

<p align="center">
  <img src="docs/assets/image/04-dockerhub-image-tags.png" alt="Tags da imagem publicadas no Docker Hub">
</p>

<p align="center"><em>Repositório da imagem no Docker Hub com a tag <code>latest</code> publicada.</em></p>

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="deploy-no-kubernetes"></a>

## ☸️ Deploy no Kubernetes

O arquivo [`k8s/deployment.yaml`](k8s/deployment.yaml) define:

- um `Deployment` chamado `web`
- uma réplica da aplicação
- um `Service` exposto na porta `80`
- encaminhamento para a aplicação na porta `5000`

### Observação importante sobre ambiente local

Como o cluster roda localmente com `k3d` em ambiente WSL, o `Service` do tipo `LoadBalancer` pode ficar com `EXTERNAL-IP <pending>`.
Por isso, o método mais confiável para teste manual local é usar `kubectl port-forward`.

<p align="center">
  <img src="docs/assets/image/05-kubernetes-deployment-status-terminal.png" alt="Estado do deployment no Kubernetes">
</p>

<p align="center"><em>Estado do deployment, pods, service e imagem em execução no cluster local.</em></p>

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="pipeline-cicd"></a>

## 🔄 Pipeline CI/CD

O workflow em [`.github/workflows/main.yml`](.github/workflows/main.yml) está dividido em dois jobs:

### Job `CI` — Build e Push da imagem Docker

- roda em `ubuntu-latest`
- valida se as credenciais do Docker Hub estão configuradas
- faz autenticação no registry
- builda a imagem a partir de `./src`
- publica duas tags:
  - `v${{ github.run_number }}`
  - `latest`

### Job `CD` — Deploy no Kubernetes

- roda em `self-hosted`
- depende do sucesso do job `CI`
- atualiza o manifesto com a tag versionada da imagem
- aplica o deploy com `kubectl apply`
- valida a atualização com `kubectl rollout status`

### Por que esse desenho foi adotado?

- separa responsabilidades entre build e deploy
- usa runner hospedado para a etapa de `CI` e runner local para a etapa de `CD`
- aproxima o `kubectl` do cluster que realmente recebe a atualização
- reduz atrito operacional em ambientes locais ou privados

<p align="center">
  <img src="docs/assets/image/03-actions-workflow-jobs-success.png" alt="Workflow com CI e CD em verde">
</p>

<p align="center"><em>Detalhe do workflow com as etapas de CI e CD concluídas com sucesso.</em></p>

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="teste-manual-da-aplicacao"></a>

## 🧪 Teste manual da aplicação

Este fluxo foi validado manualmente no cluster local.

### 1. Confirmar o rollout

```bash
kubectl rollout status deployment/web
```

### 2. Verificar a imagem em execução

```bash
kubectl get deployment web -o jsonpath='{.spec.template.spec.containers[0].image}'
```

### 3. Abrir acesso local ao Service

```bash
kubectl port-forward svc/web 18080:80
```

### 4. Testar a aplicação via HTTP

```bash
curl -i http://127.0.0.1:18080
```

### Resposta esperada

```http
HTTP/1.1 200 OK
Content-Type: text/plain

Aplicacao exemplo
```

### Diagnóstico adicional útil

```bash
kubectl get deployment web -o wide
kubectl get pods -l app=web -o wide
kubectl get svc web -o wide
```

<p align="center">
  <img src="docs/assets/image/06-application-browser-response.png" alt="Aplicação respondendo no navegador">
</p>

<p align="center"><em>Aplicação acessada no navegador por meio de <code>port-forward</code>.</em></p>

<p align="center">
  <img src="docs/assets/image/07-application-http-200-terminal.png" alt="Resposta HTTP 200 no terminal">
</p>

<p align="center"><em>Validação da resposta HTTP da aplicação no terminal.</em></p>

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="troubleshooting"></a>

## 🛠️ Troubleshooting

Alguns problemas recorrentes neste fluxo e como diagnosticar cada um deles rapidamente:

### 1. Falha de autenticação no Docker Hub

Se o job de build falhar no passo de login, confirme se:

- `DOCKERHUB_USERNAME` aponta para a conta correta.
- `DOCKERHUB_TOKEN` está salvo como secret do repositório.
- o valor configurado é um `Personal Access Token`, principalmente quando a conta usa `2FA`.

Teste local recomendado:

```bash
echo 'SEU_TOKEN' | docker login -u las43 --password-stdin
```

### 2. Job de deploy preso em `queued`

Quando o job `CD` usa `self-hosted`, esse comportamento normalmente indica que o runner não está online ou não está disponível para receber novos jobs.

Comando de verificação:

```bash
cd ~/actions-runner-primeira-pipeline-cicd
sudo ./svc.sh status
```

### 3. A imagem não foi atualizada no cluster

Se o pipeline termina com sucesso, mas o cluster continua usando uma versão antiga, vale confirmar a imagem aplicada e o estado do rollout:

```bash
kubectl get deployment web -o jsonpath='{.spec.template.spec.containers[0].image}' && echo
kubectl rollout status deployment/web
kubectl get pods -l app=web -o wide
```

### 4. O serviço não abre externamente

Em ambientes locais com `k3d` ou `WSL`, um `Service` do tipo `LoadBalancer` pode não ficar acessível diretamente. Nesses casos, o teste mais estável é via `port-forward`:

```bash
kubectl port-forward svc/web 18080:80
curl -i http://127.0.0.1:18080
```

### 5. A aplicação subiu, mas responde com erro

Quando o pod está `Running`, mas a aplicação não responde como esperado, o próximo passo é inspecionar logs e eventos do pod:

```bash
kubectl logs deployment/web
kubectl describe pod -l app=web
```

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="evidencias-de-validacao"></a>

## ✅ Evidências de validação

- [x] Pipeline de `CI` executando build e push da imagem com sucesso
- [x] Pipeline de `CD` executando deploy automático no cluster local
- [x] Runner self-hosted configurado para o repositório
- [x] Rollout validado no Kubernetes
- [x] Aplicação respondendo com `HTTP 200 OK`
- [x] Teste manual confirmado via `port-forward`

### Resultado operacional esperado

- a `main` dispara a pipeline automaticamente
- uma nova imagem é publicada no Docker Hub
- o cluster recebe a nova versão
- a aplicação permanece acessível localmente para validação

<p align="center">
  <img src="docs/assets/image/08-self-hosted-runner-service-status.png" alt="Status do self-hosted runner">
</p>

<p align="center"><em>Runner self-hosted responsável pela etapa de deploy no cluster local.</em></p>

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="decisoes-tecnicas-do-projeto"></a>

## 🧭 Decisões técnicas do projeto

As principais decisões adotadas neste repositório foram:

- **Aplicação enxuta**: a aplicação HTTP foi mantida simples para destacar o fluxo de build, publicação e deploy.
- **CI separado de CD**: o job de `CI` empacota e publica a imagem, enquanto o job de `CD` aplica a nova versão no cluster.
- **Runner self-hosted no deploy**: o cluster é local, então a etapa de `CD` precisa rodar em uma máquina com acesso direto ao `kubectl`.
- **Versionamento de imagem**: o pipeline publica `latest` e também uma tag versionada com `github.run_number`.
- **Validação pós-deploy**: o workflow confirma o rollout e o ambiente ainda pode ser validado manualmente via `port-forward` e `curl`.

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="como-executar-localmente"></a>

## 🚀 Como executar localmente

### Rodar a aplicação em Go

```bash
cd src
go run .
```

### Buildar a imagem Docker

```bash
docker build -t primeira-pipeline-cicd:local ./src
```

### Rodar a imagem localmente

```bash
docker run --rm -p 5000:5000 primeira-pipeline-cicd:local
```

### Testar localmente fora do cluster

```bash
curl http://127.0.0.1:5000
```

### Acessar o fluxo no GitHub

- [GitHub Actions](https://github.com/brodyandre/primeira-pipeline-cicd/actions)
- [Workflow principal](.github/workflows/main.yml)
- [Manifesto Kubernetes](k8s/deployment.yaml)

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

<a id="proximas-evolucoes"></a>

## 📈 Próximas evoluções

Algumas melhorias que deixariam este projeto ainda mais forte:

- adicionar testes automatizados da aplicação antes do build da imagem
- incluir `livenessProbe` e `readinessProbe` no `Deployment`
- adotar `Helm` ou `Kustomize` para gerenciamento de manifests
- separar ambientes de `dev`, `staging` e `prod`
- publicar métricas e logs estruturados
- automatizar estratégia de rollback

<p align="right"><a href="#indice">⬆️ Voltar ao índice</a></p>

---

<div align="center">
  <strong>Repositório focado em build, publicação e deploy automatizado em Kubernetes local.</strong><br>
  <sub>Um fluxo enxuto para documentar integração entre aplicação, container, pipeline e cluster. ✨</sub>
</div>
