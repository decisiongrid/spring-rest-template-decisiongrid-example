# Java Spring Example
Example call to decision grid api using spring rest template.
This simple project shows how can you call decision grid api mainly
- init rest template
- add authorization headers
- execute sample rule on decisiongrid 

## How to execute this example
Clone this repo to your computer
````bash
git clone 
````

Add your sample rule id (every account on decision grid has this sample rule by default) and bearer token to Main class. 
````java

````


Execute main method in Main class and see the result

## Decision grid call example

### prepare header with token authorization
Every call to decision grid has to containg authorization header with bearer token.

````java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
headers.set("Authorization", "Bearer " + bearerToken);
````

### call api with json in string 

````java
//prepare input object
String jsonInput = "{\"delivery\":{\"distance\":40,\"tariff\":\"basic\"},\"pack\":{\"weight\":4,\"longestSide\":50}}";
        

HttpEntity<String> entity = new HttpEntity<String>(jsonInput, headers);
ResponseEntity<String> response = restTemplate.exchange(formatUrl(connectionUrl, ruleId, version), HttpMethod.POST, entity, String.class);
        
````

### call api with java model

````java
// register object mapper to rest template
final ObjectMapper objectMapper = new ObjectMapper();
MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter(objectMapper);        
this.restTemplate.getMessageConverters().add(converter);

//prepare input object
ExampleRuleInput inputModel =

//call api
HttpEntity<ExampleRuleInput> entity = new HttpEntity<ExampleRuleInput>(inputModel, headers);
final List<ExampleRuleOutput> output = restTemplate.postForObject(formatUrl(connectionUrl, ruleId, version), entity, List.class);
        
````

## Troubleshooting
You can encounter certificate validation problem

PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
javax.net.ssl.SSLHandshakeException: PKIX path building failed

Problem is caused by missing root certificate in standard OpenJDK keystore. 
You need to download the root certificate from api.decisiongrid.io web and install it into keystore with following command:
``shell
keytool -import -trustcacerts -file /path/to/cert.cer -alias somealiasforcert -keystore $JAVA_HOME/jre/lib/security/jssecacerts -storepass changeit
``
If for some reason you don't want to add root certificate, adding intermediate certificate should do the trick as well.
