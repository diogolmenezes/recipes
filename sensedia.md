# Sensedia Custom Interceptors 

### Abort Request
```
$call.stopFlow = true;  
$call.decision.setAccept( false );  
$call.response = new com.sensedia.apigateway.services.ApiResponse();
$call.response.setStatus( ## STATUS ## );
```

### Get Context Variables
```
const xpto = $call.contextVariables.get("## NAME VARIABLE ##");
```

### Get Context Variables
```
const xpto = String( $call.environmentVariables.get("## NAME VARIABLE ##");
```

### Get/Set Response Payload
```
const payload = $call.response.getBody().getString( "utf-8" );
```
```
$call.response.getBody().setString( $jdom.stringify(## NEW BODY ##), "utf-8" );
```

### Get App Extra Info
```
const info = $call.app.extraInfo.get("## NAME EXTRA INFO KEY ##")
```

### Get URL
```
const url = $call.request.getRequestedUrl().toString();
```

### Get Query Param
```
const param = $request.getQueryParam("## NAME PARAM ##");
```

### Create new URI
```
const uri = new java.net.URI(## STRING URI ##);
```

### Get/Set Destination Uri
```
const destination = $call.destinationUri.toString();
```
```
const uri = new java.net.URI(## URI ##);
$call.setDestinationUri(uri);
```

### Get Path Param
```
const param = $call.pathParam.get(## PARAM ##);
```

### Get Access Token Extra Info
```
const info = $call.accessToken.extraInfo;
const cpf = info.get("cpf");
```

## Gerando um Sensedia Auth

    Entre em Access Tokens no Manager e crie um token com o extra fields - userId: -1, associe ele ao plano API Manager 3.0 ( Integration Plan )

## Revogando um token

    Clique em ... na interface depois API Manager Browser para pegar a base URL, depois de um request de DELETE para
    
    DELETE baseUrl/access-tokens/ + tokenParaRevogar

## Incluindo atributos adicionais na resposta

    Incluir um interceptor de response, que adiciona os campos extras antes de devolver

    ```javascript
    try {
            if($response.getStatus() == 200) { 
                const body =  JSON.parse($response.getBody().getString('utf-8'));
                body.teste = 'isso é um teste'
                $call.response.getBody().setString($json.stringify(body), "utf-8");
            }
        }
        catch(e) {
            $call.tracer.trace('Erro =>', e);
        }
    ```