# Boas práticas para o desenvolvimento de RESTful API's

### 1. Requisitos fundamentais

* Simples, intuitiva e consistente
* Endereçável (possibilidade de acesso direto através de sua URL)
* Eficiente

### 2. URL’s e métodos HTTP

Uma arquitetura REST deve separar sua API em recursos lógicos, ou seja, é uma arquitetura orientada a recursos. Portanto, os recursos devem ser identificados dentro da sua aplicação através de URL's e acessados pelos cliente através de uma interface uniforme.

Nesse cenário temos algumas boas práticas:
* Recursos devem ser nomes, não verbos. Os nomes devem ser preferencialmente no plural.
* HTTP GET deve ser seguro, ou seja, não pode alterar estado. Deve ser utilizado apenas para consulta.
* HTTP DELETE e HTTP PUT devem ser idempotentes, ou seja, executado diversas vezes devem sempre produzir o mesmo resultado.
* HTTP PUT deve ser utilizado para atualização de recursos já existente ou ainda para criação de novos quando se conhece o identificador do recurso.
* HTTP DELETE deve ser utilizado para remover recurso já existentes e não deve falhar se o recurso não existir. Preferencialmente, não deve ser utilizado para remover recursos que representem listas.
* HTTP POST não é um método idempotente e nem seguro, ou seja, pode alterar estado e pode ter resultados distintos quando executado várias vezes. Normalmente é utilizado para criar sub-recursos de um recurso pai ou ainda para executar ações
* Utilizar sub-recursos para definir relacionamento entre 1xN
* Opcionalmente pode se utilizar o método HTTP PATCH para atualizar um recurso parcialmente

### 3. Documentação

Uma boa API deve possuir uma boa documentação. Portanto, sempre que possível utilize ferramentas para gerar uma documentação clara de sua API.

### 4. Negociação de conteúdo

Usar os headers "Content-Type" e "Accept" para o formato da requisição e a lista de formatos aceitos como resposta respectivamente.

### 5. Versionamento

Sempre que possível versione sua API, de preferência a versão deve vir na própria URL.

### 6. Filtros

Um recurso que retorne uma lista de valores deve suportar a possibilidade de utilizar filtros para reduzir a quantidade de resultados retornados. Esses filtros podem ser parâmetros únicos que correspondam aos campos do recurso que podem ser utilizados como filtro.

### 7. Ordenação

O fornecedor da API também deve suportar a ordenação dos resultados de uma lista, através do parâmetro sort.

### 8. Busca

Em alguns cenários apenas os filtros não são suficientes. Nesses casos se faz necessário a implementação de busca de texto no interior do corpo da representação, que pode ser implementado com a inclusão de um parâmetro q.

Pode ser criado também um alias para consultas frequentes que inclua filtros, ordenação e/ou busca, como por exemplo busca de registros mais recentes de um determinado estado.

### 9. Resposta parcial

Normalmente o consumidor da API não precisa da representação do recurso completa, mas apenas parte dos campos.

Assim, o provedor da API deve permitir que o cliente informe uma lista de campos desejados. Isso pode ser realizado incluindo um parâmetro chamado fields onde o cliente pode informar a lista de campos a serem retornados.

### 10. Paginação

Em alguns cenários a quantidade de dados é muito grande, portanto se faz a necessário a paginação do resultado que pode ser implementado com os parâmetros offset e limit.

Deve ser retornado também uma lista de links para a próxima página, página anterior, primeira e última página. 

### 11. HATEOAS

Permite fazer o link entre recursos relacionados, possibilitando assim o late binding, ou seja, permite ao cliente a navegação entre recursos em tempo de execução como em uma página HTML.

O link pode ser parte da própria representação do recurso ou mesmo como metadado nos headers segundo a especificação Web Linking (RFC 5988).

### 12. Representação dos recursos na atualização

Para evitar a necessidade de consulta em um recurso que foi atualizado parcialmente, a resposta da requisição deve incluir a representação do recurso atualizada.

Quando for criado um novo recurso com método POST deve ser retornado HTTP 201 com o header Location e o endereço do novo recurso.

### 13. Formato da resposta

Sempre que possível utilizar formatos leves como JSON, evitando ao máximo formatos verbosos como o XML.

### 14. Compressão da resposta

A fim de reduzir a quantidade de dados trafegados remova os espaços em brancos da representação do recurso e também utiliza compressão, por exemplo gzip. Essa prática pode reduzir mais de 60% a quantidade de bytes da representação.

Para fim de debug podemos incluir o parâmetro pretty=true para identificar que o formato será retornado sem compressão e com todos os espaços em branco.

### 15. Sobrescrever métodos HTTP

Alguns cliente HTTP apenas suportam a utilização dos métodos HTTP GET e HTTP POST e portanto deve ser possível identificar outros métodos HTTP através do header X-HTTP-Method-Override

### 16. Rate limit

Para evitar o abuso das requisições é importante implementar  o mecanismo de rate limit definido pela RFC 6585. 

### 17. Autenticação / Autorização

Uma RESTful API deve ser stateless, porém é necessário que os recursos sejam protegidos de acesso indevido.

Uma boa opção para atender esses requisitos seria implementar uma autenticação por meio de token.

### 18. Caching

Para reduzir a quantidade de dados trafegados é imprescindível o uso de cache. O cache pode ser validado por conteúdo (Etag) ou timestamp (Last-Modified).

Portanto, o cliente deve incluir os headers apropriados e deve saber tratar a resposta HTTP 304 Not Modified. O provedor da API deve saber interpretar os headers de cache e retornar HTTP 304 quando necessário, portanto deve ser definido um algoritmo para cálculo da Entity Tag e também deve ser armazenado sempre a data da última modificação do recurso. 

### 19. CORS

Uma API pública ou mesmo em uma arquitetura de microserviços deve ser permitido a realização de requisições AJAX através do navegador de domínios diferentes. Assim, deve ser implementado o mecanismo de Cross-Origin Resource Sharing definido pela W3C.

### 20. Tratamento de erros

Utilizar códigos padrões do protocolo HTTP conforme lista abaixo:

* 200: Sucesso
* 201: Recurso criado
* 204: Sem conteúdo
* 304: Não modificado (caching)
* 400: Bad Request
* 401: Não autorizado
* 403: Não permitido
* 404: Não encontrado
* 405: Método não suportado
* 410: Recurso não disponível (versões da API antigas)
* 415: Mídia não suportada
* 422: Entidade não processada (erros de validação)
* 429 Excesso de requisições (rate limiting)
* 500 Erros desconhecido
* 503 Serviço não disponível

O payload do recurso pode incluir um código específico da aplicação para permitir aos clientes se recuperar do erro e uma descrição para humanos realizarem o troubleshooting.
