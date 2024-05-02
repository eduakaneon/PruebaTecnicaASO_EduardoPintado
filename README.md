# Proyecto Servicio Hello World

Este proyecto implementa un servicio para realizar operaciones de simulación en la tienda en línea.

## Lógica del Servicio

El servicio `SrvHelloWorld` contiene la lógica para manejar las solicitudes HTTP POST. El método `data` maneja las solicitudes POST y realiza la validación de datos antes de pasar la información al negocio. Este servicio incluye un path param que sera insertado mediante la url de la request de Postman.

```java
    @Override
    @POST
    @Path("/simulations/{nuip}")
    @Produces({MediaType.APPLICATION_JSON})
    @Consumes({MediaType.APPLICATION_JSON})
    @SMC(registryID = "SMC000000053", logicalID = "simulations")
    public ServiceResponseCreated<DataOut> data(@PathParam("nuip") String nuip, @Valid Data data) {
        data.setNuip(nuip);
        ISrvHelloWorldMapper map = Mappers.getMapper(ISrvHelloWorldMapper.class);
        BData bData = map.mapInData(data);
        BDataOut bDataOut = business.bussinesData(bData);
        DataOut dataOut = map.mapOutData(bDataOut);
        return ServiceResponseCreated.data(dataOut).build();
    }
}
```

## Capa de Acceso a Datos

El componente `Dao` contiene la lógica para mapear los datos de entrada a datos de salida.

## DTOs (Objetos de Transferencia de Datos)

Se utilizan los siguientes DTOs para transportar datos entre las capas:

### DTO de Entrada

```java
@Getter
@Setter
public class Data {
    private String id;
    private String nuip;
    @Valid
    private Details details;
}
```

### DTO de Salida

```java
@Getter
@Setter
public class DataOut {
    private String id;
    private String nuip;
    private Details details;
}
```
### Ejemplo request Postman

URL: http://localhost:7500/TechArchitecture/helloWorld/v0/simulations/123456789
JSON:
{
  "details": {
    "offerType": "CARD_HOLDER",
    "limitAmount": {
      "amount": 200000,
      "currency": "EUR"
    },
    "product": {
      "id": "TDC",
      "subproduct": {
        "id": "AV"
      }
    }
  }
}
