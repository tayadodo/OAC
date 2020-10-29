# OAC embedding project

[original article](https://medium.com/@insight2action/oracle-analytics-cloud-oac-embedding-public-user-access-part-1-5fb0f513508a)

In this section we will talk how to embed your cool dashboars which you rescently designein in Oracle Analytics cloud into your web-application.

First: let's take a look what do we need so far:
1. Web-server, running our web-application
2. Provisioned Oracle Cloud Account 
3. Oracle Analytics Cloud (OAC) instance with at least one dashboard

## Solution overview
pic

## Script for function

Scrit purpose is to generate request to obtain access token to IDCS and to parse respond. The outcome of the script is token

```python
import io
import json
import logging
import requests, os

from fdk import response

url = os.environ["url"]
header = {"authorization": "Basic <User Secret>", "content-type": "application/x-www-form-urlencoded;charset=UTF-8"}
body = "grant_type=password&username=<User Name>&password=<Password>&scope=<Scope from IDCS>"


def handler(ctx, data: io.BytesIO = None):
    try:
#        body = json.loads(data.getvalue())
        res = requests.post (url, data = body, headers=header)
    except (Exception, ValueError) as ex:
        logging.getLogger().info('error parsing json payload: ' + str(ex))

    logging.getLogger().info("Inside Python Hello World function")
    return response.Response(
        ctx, response_data=json.dumps(res.json()),
        headers={"Content-Type": "application/json"}
    )
```

<IDCS ref>
<User Secret>
<User Name>
<Password>
<Scope from IDCS>
