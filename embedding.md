# OAC embedding project

[original article](https://medium.com/@insight2action/oracle-analytics-cloud-oac-embedding-public-user-access-part-1-5fb0f513508a)

In this section we will talk how to embed your cool dashboars which you rescently designein in Oracle Analytics cloud into your web-application.

First: let's take a look what do we need so far:
1. Web-server, running our web-application
2. Provisioned Oracle Cloud Account 
3. Oracle Analytics Cloud (OAC) instance with at least one dashboard

## Solution overview
pic

## IDCS Instruction
correct in the article
!!better to create user with minimum user privileges

curl to test idcs access



## Script for function

Scrit purpose is to generate request to obtain access token to IDCS and to parse respond. The outcome of the script is IDCS responce in json format

```python
import io
import json
import logging
import requests, os

from fdk import response

#url = "https://<IDCS url>.identity.oraclecloud.com/oauth2/v1/token"
url = os.environ["url"]
header = {"authorization": "Basic <User Secret>", "content-type": "application/x-www-form-urlencoded;charset=UTF-8"}
body = "grant_type=password&username=<User Name>&password=<Password>&scope=<Scope from IDCS>"


def handler(ctx, data: io.BytesIO = None):
    try:
#       body = json.loads(data.getvalue())
        res = requests.post (url, data = body, headers=header)
    except (Exception, ValueError) as ex:
        logging.getLogger().info('error parsing json payload: ' + str(ex))

    logging.getLogger().info("Inside Python Hello World function")
    return response.Response(
        ctx, response_data=json.dumps(res.json()),
        headers={"Content-Type": "application/json"}
    )
```

`<IDCS url>`
`<User Secret>`
`<User Name>`
`<Password>`
`<Scope from IDCS>`
    
## HTML page
```html
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML//EN">
<html>
  <head>
   <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
   <title>Standalone DV Embed Demo Using Token</title>
   <script src="https://<OAC dev URL>/public/dv/embedding/v1/standalone/embedding.js?token=true" type="application/javascript">
   </script>
  </head>
 <body>
   <B>Standalone embedded project test</B>
   <div style="width: calc(100% — 40px); height: (100% — 40px); border: 1px solid black; padding: 10px;" >
    <oracle-dv
    project-path="<Project Path>"
    active-page="canvas"
    active-tab-id="1">
    </oracle-dv>
   </div>
 <script>
  var Url = "https://<API GATEWAY url>.apigateway.<AD>.oci.customer-oci.com/token/get_token"

  var token_request = new XMLHttpRequest();
  token_request.open("GET", Url, false);
  token_request.send(null);
  {
  if (token_request.status = 200) {
    var idcs_token = JSON.parse(token_request.response).access_token;
    requirejs(['jquery', 'knockout', 'obitech-application/application'],
    function($, ko, application) {
    application.setSecurityConfig("token", {tokenAuthFunction:
    function(){
    return idcs_token;
  }
 });
 ko.applyBindings();
 }
 );
 } else {
   console.log('error ${token_request.status} ${token_request.statusText}')
 }
 }  </script>
 </body>
</html>
```

`<API GATEWAY url>`
`<AD>` like us-ashburn-1
`<Project Path>`
`<OAC dev URL>`
