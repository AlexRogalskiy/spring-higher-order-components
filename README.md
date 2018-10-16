## Spring HOC

##### Why you should use it?

#### @EnableMailing

This component give you simple API to send emails using Amazon SES service. 

###### Configuration

- Provide **verified** sender email address ``spring-hoc.mail.sender-email-address``
- Provide AWS credentials ``spring-hoc.aws.access-token``, ``spring-hoc.aws.secret-key``, ``spring-hoc.aws.region``

Spring HOC will automatically create for you Amazon SES component if bean doesn't exit.

###### How to send e-mail?

Use ``EmailRequest`` step builder to create request.

```
EmailRequest.builder()
            .to("hello@jpomykala.me")
            .subject("Hey, I just met you and this is crazy")
            .body("But here's my number, so call me maybe")
            .build();
```

Now it's time to send email. You have 2 options here.
- ``@Autowire MailService`` and invoke ``sendEmail(EmailRequest)``.
- Publish ``EmailRequest`` using ``ApplicationEventPublisher``

That's all!

#### @EnableCors [alpha]

This annotation adds filter with which handle CORS. Right now you can configure only allowed origins using ``application.yml`` See example configuration below.

#### @EnableRequestLogging

Adds logging requests, populate MDC with:
- user (IP address by default)
- requestId (UUID by default).

```
@Bean
public LoggingFilter loggingFilter(LoggingFilterFactory loggingFilterFactory){
  return loggingFilterFactory
          .withPrincipalProvider() // [optional] PrincipalProvider implementation 
          .withRequestIdProvider() // [optional] RequestIdProvider implementation
          .withCustomMdc("user", "[u:%s][rid:%s]") // [optional] MDC key, String.format()
          .createFilter();
}
```

#### @EnableFileUploading

This annotation autoconfigures Amazon S3 component if bean doesn't exit.

``@Autowire UploadService`` gives you ability to upload files using overloaded methods:
- ``void upload(@NotNull UploadRequest uploadRequest)``
- ``void upload(@NotNull MultipartFile file)``
- ``void upload(@NotNull MultipartFile file, @NotNull String path)``
- ``void upload(byte[] bytes, String fileKey)``
- ``void upload(byte[] bytes, String fileKey, ObjectMetadata metadata)``
- ``String upload(byte[] bytes)`` // path is autogenerated and returned (SHA256)

##### example ``application.yml`` configuration

```
spring-hoc:
  aws:
    access-token: xxxxxxxx
    secret-key: xxxxxxxx
    region: eu-west-1
  cors:
    allowed-origins:
      - "http://localhost:3000"
  mail:
    sender-email-address: xxxxx@xxxx.com    
```
