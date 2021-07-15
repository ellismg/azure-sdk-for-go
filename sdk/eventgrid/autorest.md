# Azure EventGrid Protocol Layer

> see https://aka.ms/autorest

## Configuration

```yaml
require: C:\dd\azure\azure-rest-api-specs\specification\eventgrid\data-plane\README.md
module: github.com/Azure/azure-sdk-for-go/sdk/eventgrid/azeventgrid
output-folder: azeventgrid
credential-scope: none
license-header: MICROSOFT_MIT_NO_VERSION
file-prefix: zz_generated_
modelerfour:
  lenient-model-deduplication: true
```

## Customizations

### Mark a parameter AutoRest says must be required as required.

```yaml
directive:
  - from: swagger-document
    where: $.definitions.MediaJobOutput
    transform: >
      $.required.push("@odata.type");
```

### Use the "EventData" suffix on the Azure Resource Manager Event types, instead of just "Data"

```yaml
directive:
  - from: swagger-document
    where: $.definitions
    transform: >
      ["Write", "Delete", "Action"].forEach(action => {
        ["Success", "Failure", "Cancel"].forEach(status => {
          if ($[`Resource${action}${status}Data`]) {
            $[`Resource${action}${status}Data`]["x-ms-client-name"] = `Resource${action}${status}EventData`;
          }
        });
      });
```

### Remove the "Properties" suffix from some object types.

It's obvious they are properties and the type names read better without the suffix.

```yaml
directive:
  - from: swagger-document
    where: $.definitions
    transform: >
      for (const definition in $) {
        if (definition.endsWith("Properties") && $[definition].properties !== undefined) {
          const cleanName = definition.substring(0, definition.length - "Properties".length);
          $[definition]["x-ms-client-name"] = cleanName;
        }
      }
```