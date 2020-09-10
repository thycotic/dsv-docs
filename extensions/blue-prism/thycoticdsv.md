[title]: # (Blue Prism, VBO ThycoticDSV)
[tags]: # (DevOps Secrets Vault,DSV)
[priority]: # (2)

## Description VBO ThycoticDSV

The VBO consists of four methods that implement the REST API calls to work with DSV. If necessary, they can be increased to implement all API calls described in the documentation [REST API for DSV](https://dsv.thycotic.com/api).

ThycoticDSV usage internal VBO from Blue Prism

> BPA Object - Utility - Collection Manipulation
>
> BPA Object - Utility - Environment
>
> BPA Object - Utility - General
>
> BPA Object - Utility - HTTP
>
> BPA Object - Utility - JSON
>
> BPA Object - Utility - Strings
>
> BPA Object - Webservices - REST
> 
---

### GetToken

The method implements an REST API [Authorization](https://dsv.thycotic.com/api#operation/token) call to obtain an access token. Authorization is carried out by ``client_id`` and ``client_secret`` with ``grant_type=client_credentials``.

> Input parameters: 
> ``config`` - name record in internal credential vault, usage ***ThycoticDSV***
> 
> ``ThycoticDSV(client_id)`` - string client_id , get value from internal credential store, however you may assign to variable.
>
> ``ThycoticDSV(client_secret)`` - string client_secret , get value from internal credential store, however you may assign to variable.
> 
> ``ThycoticDSV(server_url)`` -  string server_url , get value from internal credential store, however you may assign to variable.

> Output parameters: 
>
> ``Token`` - string  Token , set Bearer authorization token.
> 

![GetToken](Images/GetToken.png)

---

### ListSecret

This method implements an REST API [List Secret](https://dsv.thycotic.com/api#operation/listSecretPaths). For authorization session call method ``GetToken`` include in current VOB.

> Input parameters: 
> 
> ``config`` - name record in internal credential vault, usage ***ThycoticDSV***
>
> ``path`` - path for DSV where find secrets.

> Output parameters:
>
> ``secretArray`` - return collection with find secret name.

![ListSecret](Images/listSecret.png)

---

### getDataField

The method implements an REST API [Get Secret](https://dsv.thycotic.com/api#operation/getSecret) and parse section ``data`` for search field defined in input parameters (ex. `username`, `passowrd`, `machine` and other).

For authorization session call method ``GetToken`` include in current VOB.

Input parameters:

>
>``config`` - name record in internal credential vault, usage ***ThycoticDSV***
> 
> ``Path`` - string value, ***path*** secret.
> 
> ``Field`` - string value, name field data in section ``data`` secret, can be any, set when creating a secret.
> 
> Output parameters:
> 
> ``ReturnValue`` - string value, secret field value found.

![GetDataFild](Images/GetDataField.png)