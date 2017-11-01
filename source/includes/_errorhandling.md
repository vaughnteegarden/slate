

# Error Handling

```json
{
  "errors":[
    {
      "type":"<error_type>",
      "code":"<error_code>",
      "message":"<error_message>"
    }
  ]
}
```
All SDK methods use a uniform error format, as shown at the right. Possible values are:

|Type|Error Code|Message|
|---|---|---|
|Authentication|1|Access is denied.|
|Registration|2|Registration failed.|
|Bad Request|3|Missing required parameters in request body. Required: ((required_parameters))|
|Not Found|4|Requested resource not found|
|Bad Request|5|Incorrect or malformed parameters|
|Bad Request|6|((system message))|
|Service Unavailable|7|Our apologies for the temporary inconvenience. The requested URL generated 503 Service Unavailable error due to overloading or maintenance of the server.|
