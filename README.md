# Proyecto Servicio Hello World

Este proyecto implementa un servicio para realizar operaciones de simulación.

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

El componente `DAO` contiene la lógica para mapear los datos de entrada a datos de salida.

## DTOs (Objetos de Transferencia de Datos)

Se utilizan los siguientes DTOs para transportar datos entre las capas(Facade, Bussiness y DAO):

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
### Validaciones:

Se han realizado distintas validaciones en las clases se aportan sus etiquetas y funcionalidades:

 - @Valid = Que se inserte un tipo de dato valido.
 - @Pattern(regexp = "[A-Z_]+") = Que se utilizen mayusculas.
 - @NotNull = Que no se introduzcan valores nulos.
 - @NotEmpty = Que no se introduzcan valores vacios.
 - @Pattern(regexp = "TDC") = Que el valor sea igual a TDC.
 - @Size(max = 2) = Que el maximo numero de caracteres sea 2.
 

### Ejemplo request Postman

- URL: http://localhost:7500/TechArchitecture/helloWorld/v0/simulations/123456789 (Este ultimo dato es el valor del {nuip})
- JSON:
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

### Funcionamiento

1. Arrancar el contenedor de Docker.
2. Situar el proyecto en la siguiente ruta: /ASO-APX/ARQ_ASO/ARQ_ASO/Development/SARrc_Projects
3. Realizar configuraciones del entorno de trabajo y asegurarse de que la version de Java sea la 1.8 y que los settings esten bien configurados con la ruta de tu equipo.
4. Hacer clean install.
5. En el terminal ir a la siguiente ruta: /ASO-APX/ARQ_ASO/ARQ_ASO/Development/SARrc_Projects/PruebaTecnica_ASO_EduardoPintado/asoexample_first_service/target
6. Ejecutar por orden los siguientes comandos:
- aso-cli deploy helloworld-facade.jar asoContainer --reload=false
- aso-cli deploy helloworld.jar asoContainer --reload=false
- aso-cli deploy helloworld-sn.jar asoContainer --reload=true
7. Esperar unos segundos y hacer la solicitud en Postman añadiendo un body de tipo JSON y con la URL antes proporcionada.
8. Comprobar que la respuesta sea la esperada: 201 POST, 204 PUT.
