# MovieMania: arquitetura DDD

Aluna: Joice de Oliveira Fernandes  
Disciplina: Projeto e Arquitetura de Software

## Domínio de aplicação

O MovieMania é uma plataforma web em que usuários cadastrados publicam filmes com título, descrição e imagem, exploram um feed com os filmes de outras pessoas, consultam perfis públicos, curtem e comentam. O domínio gira em torno de três assuntos: quem são os usuários, quais filmes existem e como as pessoas interagem com eles.

## Origem

O projeto de Programação Web foi construído em camadas técnicas (Rota, Controller, Service, Model). Este repositório mostra a mesma aplicação reorganizada em torno do domínio (DDD). Ele contém só a estrutura e a documentação, sem código-fonte.

## Contextos delimitados (Bounded Contexts)

| Contexto | Responsabilidade | Conceitos principais |
|---|---|---|
| identity | Quem é o usuário | User, cadastro, login, perfil, perfil público |
| catalog | Os filmes | Movie, publicar, editar, excluir, feed, Meus Filmes |
| engagement | A interação social | Like, Comment, contadores, filmes curtidos |
| shared | Peças comuns | Erros, formato de resposta, middlewares |

## Camadas de cada contexto

| Camada | O que contém | Depende de |
|---|---|---|
| domain | Entidades, regras de negócio, interfaces (portas) dos repositórios | Ninguém |
| application | Casos de uso que orquestram o domínio | domain |
| infrastructure | Sequelize, JWT, bcrypt, armazenamento de imagens | domain |
| interfaces | Rotas, controllers e validators (HTTP) | application |

Regra de dependência: as setas apontam para dentro. O domínio não conhece Express, Sequelize nem o sistema de arquivos.

## Mapa: código atual para a nova estrutura

| Código atual | Novo lugar |
|---|---|
| user/userModel.js | identity/domain e identity/infrastructure |
| user/userService.js (registro, login, perfil) | identity/application |
| userRoutes, userController, userValidator | identity/interfaces |
| config/jwt.js e uso do bcrypt | identity/infrastructure |
| movie/movieModel.js | catalog/domain e catalog/infrastructure |
| movie/movieService.js e getFeed | catalog/application |
| movieMulter.js e remoção de arquivos com fs | catalog/infrastructure |
| movieRoutes, movieController, movieValidator | catalog/interfaces |
| like/ e comment/ (models) | engagement/domain e engagement/infrastructure |
| likeService e commentService | engagement/application |
| rotas e controllers de like e comment | engagement/interfaces |
| apiResponse, asyncHandler, errorHandler, auth | shared |

## Regras de negócio que pertencem ao domínio

- Uma pessoa só pode curtir um filme uma vez.
- Só o dono pode editar ou excluir o filme.
- Um comentário não pode ser vazio e tem no máximo 500 caracteres.
- Excluir um filme remove suas curtidas e seus comentários.
- Os contadores de curtidas e comentários devem ficar consistentes com as tabelas.

## Estrutura de pastas

```text
src/
  identity/       domain, application, infrastructure, interfaces
  catalog/        domain, application, infrastructure, interfaces
  engagement/     domain, application, infrastructure, interfaces
  shared/         errors, http, middlewares
docs/
```