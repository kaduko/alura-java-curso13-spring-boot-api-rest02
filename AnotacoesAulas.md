# alura-java-curso13-spring-boot-api-rest02
Curso de Spring Boot 3: aplique boas práticas e proteja uma API Rest

---
## 📄 Aula 01 - - Boas práticas na API

Tratamento do retorno da API nas requisições
- Existem boas práticas para os retornos das requisições.
- Por exemplo, para retorno com sucesso, normalmente é retornado código http 200

Obs.:
- Não é recomendado devolver e receber entidades JPA no Controller

Códigos do protocolo HTTP
- O protocolo HTTP (Hypertext Transfer Protocol, RFC 2616) é o protocolo responsável por fazer a comunicação entre o 
cliente, que normalmente é um browser, e o servidor. Dessa forma, a cada “requisição” feita pelo cliente, o servidor 
responde se ele obteve sucesso ou não. Se não obtiver sucesso, na maioria das vezes, a resposta do servidor será uma 
sequência numérica acompanhada por uma mensagem. Se não soubermos o que significa o código de resposta, dificilmente 
saberemos qual o problema que está acontecendo, por esse motivo é muito importante saber quais são os códigos HTTP e 
o que significam.
- Categoria de códigos
  - Os códigos HTTP (ou HTTPS) possuem três dígitos, sendo que o primeiro dígito significa a classificação dentro das 
  possíveis cinco categorias.
    - 1XX: Informativo – a solicitação foi aceita ou o processo continua em andamento;
    - 2XX: Confirmação – a ação foi concluída ou entendida;
    - 3XX: Redirecionamento – indica que algo mais precisa ser feito ou precisou ser feito para completar a solicitação;
    - 4XX: Erro do cliente – indica que a solicitação não pode ser concluída ou contém a sintaxe incorreta;
    - 5XX: Erro no servidor – o servidor falhou ao concluir a solicitação.
  - Principais códigos de erro
    - Como dito anteriormente, conhecer os principais códigos de erro HTTP vai te ajudar a identificar problemas em 
    suas aplicações, além de permitir que você entenda melhor a comunicação do seu navegador com o servidor da 
    aplicação que está tentando acessar. 
    - Error 403
      - O código 403 é o erro “Proibido”. Significa que o servidor entendeu a requisição do cliente, mas se recusa a 
      processá-la, pois o cliente não possui autorização para isso.
    - Error 404 
      - Quando você digita uma URL e recebe a mensagem Error 404, significa que essa URL não te levou a lugar nenhum. 
      Pode ser que a aplicação não exista mais, a URL mudou ou você digitou a URL errada. 
    - Error 500 
      - É um erro menos comum, mas de vez em quando ele aparece. Esse erro significa que há um problema com alguma das 
      bases que faz uma aplicação rodar. Esse erro pode ser, basicamente, no servidor que mantém a aplicação no ar ou 
      na comunicação com o sistema de arquivos, que fornece a infraestrutura para a aplicação. 
    - Error 503 
      - O erro 503 significa que o serviço acessado está temporariamente indisponível. Causas comuns são um servidor em 
      manutenção ou sobrecarregado. Ataques maliciosos, como o DDoS, causam bastante esse problema. 
  - Uma dica final: dificilmente conseguimos guardar em nossa cabeça o que cada código significa, portanto, existem 
  sites na internet que possuem todos os códigos e os significados para que possamos consultar quando necessário. 
  Existem dois sites bem conhecidos e utilizados por pessoas desenvolvedoras, um para cada preferência: se você gosta 
  de gatos, pode utilizar o HTTP Cats (https://http.cat/); 
  já, se prefere cachorros, utilize o HTTP Dogs (https://http.dog/).

Nessa aula:
- Utilizar a classe ResponseEntity, do Spring, para personalizar os retornos dos métodos de uma classe Controller;
- Modificar o código HTTP devolvido nas respostas da API;
- Adicionar cabeçalhos nas respostas da API;
- Utilizar os códigos HTTP mais apropriados para cada operação realizada na API.

---
## 📄 Aula 02 - Lidando com erros

Para saber mais sobre as propriedades do application.properties
- Common Application Properties:
https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html

Anotação @RestControllerAdvice
- Utilizamos esta anotação para indicar ao Spring que a classe anotada é uma classe utilizada no tratamento de erros.
- O tratamento de exceptions do Spring funciona tanto para checked exceptions, quanto para unchecked exceptions.
- Embora o ideal seja devolver alguma informação, deixar o método sem retorno não impede que ele seja chamado pelo 
spring.

Anotação @ExceptionHandler
- Utilizamos esta anotação para especificar que o método anotado está tratando uma determinada exception.

@RestControllerAdvice
public class TratadorDeErros {
        @ExceptionHandler(EntityNotFoundException.class)
        public void tratarErro404() {
        }
}

- Na classe acima, estamos indicando que ela é para tratamento de erros e que o método "tratarErro404" está tratando a 
exception EntityNotFoundException
- É possível receber a exception lançada no método que estamos tratando o erro, basta declarar como parâmetro na 
assinatura do método. Isso é util quando necessário capturar erros do bean validation.

Mensagens em português
- Por padrão o Bean Validation devolve as mensagens de erro em inglês, entretanto existe uma tradução dessas mensagens 
para o português já implementada nessa especificação.
- No protocolo HTTP existe um cabeçalho (header) chamado Accept-Language, que serve para indicar ao servidor o idoma de 
preferência do cliente disparando a requisição. Podemos utilizar esse cabeçalho para indicar ao Spring o idioma 
desejado, para que então na integração com o Bean Validation ele busque as mensagens de acordo com o idioma indicado.

Personalizando mensagens de erro
Uma das maneiras de personalizar as mensagens de erro é adicionar o atributo message nas próprias anotações de validação:

public record DadosCadastroMedico(
    @NotBlank(message = "Nome é obrigatório")
    String nome,

    @NotBlank(message = "Email é obrigatório")
    @Email(message = "Formato do email é inválido")
    String email,

    @NotBlank(message = "Telefone é obrigatório")
    String telefone,

    @NotBlank(message = "CRM é obrigatório")
    @Pattern(regexp = "\\d{4,6}", message = "Formato do CRM é inválido")
    String crm,

    @NotNull(message = "Especialidade é obrigatória")
    Especialidade especialidade,

    @NotNull(message = "Dados do endereço são obrigatórios")
    @Valid DadosEndereco endereco) {}
...)

- Outra maneira é isolar as mensagens em um arquivo de propriedades, que deve possuir o nome 
ValidationMessages.properties e ser criado no diretório src/main/resources:
  nome.obrigatorio=Nome é obrigatório
  email.obrigatorio=Email é obrigatório
  email.invalido=Formato do email é inválido
  telefone.obrigatorio=Telefone é obrigatório
  crm.obrigatorio=CRM é obrigatório
  crm.invalido=Formato do CRM é inválido
  especialidade.obrigatoria=Especialidade é obrigatória
  endereco.obrigatorio=Dados do endereço são obrigatórios
- E, nas anotações, indicar a chave das propriedades pelo próprio atributo message, delimitando com os caracteres { e }:
public record DadosCadastroMedico(
  @NotBlank(message = "{nome.obrigatorio}")
  String nome,

  @NotBlank(message = "{email.obrigatorio}")
  @Email(message = "{email.invalido}")
  String email,

  @NotBlank(message = "{telefone.obrigatorio}")
  String telefone,

  @NotBlank(message = "{crm.obrigatorio}")
  @Pattern(regexp = "\\d{4,6}", message = "{crm.invalido}")
  String crm,

  @NotNull(message = "{especialidade.obrigatoria}")
  Especialidade especialidade,

  @NotNull(message = "{endereco.obrigatorio}")
  @Valid DadosEndereco endereco) {}
...)

Nessa aula:
- Criar uma classe para isolar o tratamento de exceptions da API, com a utilização da anotação @RestControllerAdvice;
- Utilizar a anotação @ExceptionHandler, do Spring, para indicar qual exception um determinado método da classe de tratamento de erros deve capturar;
- Tratar erros do tipo 404 (Not Found) na classe de tratamento de erros;
- Tratar erros do tipo 400 (Bad Request), para erros de validação do Bean Validation, na classe de tratamento de erros;
- Simplificar o JSON devolvido pela API em casos de erro de validação do Bean Validation.


