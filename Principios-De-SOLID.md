# Principios SOLID en Spring Boot

![SOLID](https://tech.tribalyte.eu/wp-content/uploads/2018/02/Solid.png)

 Los principios SOLID son un conjunto de reglas y pautas que nos ayudan a diseñar sistemas más flexibles, mantenibles y fáciles de entender. Estos principios promueven una arquitectura de software sólida y modular, lo que nos permite hacer cambios con confianza y sin introducir errores inesperados.

## S - Principio de Responsabilidad Única (Single Responsibility Principle)

El principio de responsabilidad única establece que una clase o módulo debe tener una única responsabilidad. Esto significa que una clase debe tener un único motivo para cambiar. Al seguir este principio, mantenemos nuestras clases cohesivas y evitamos el acoplamiento excesivo. Esto facilita la reutilización de código y el mantenimiento a largo plazo.

Proyecto *coa-api*

En el código, podemos observar que cada controlador (LicenciaturaController, MateriaController y PlanEstudiosController) tiene métodos relacionados con las operaciones CRUD de su respectiva entidad. Esto demuestra que cada controlador tiene la responsabilidad de manejar las operaciones específicas de su entidad correspondiente.

Por ejemplo, en el LicenciaturaController, se encuentran los métodos getAllLicenciatura, createLicenciatura, updateLicenciatura y deleteLicenciatura, los cuales son responsables de gestionar las operaciones de la entidad Licenciatura. De manera similar, los otros controladores siguen el mismo patrón.

A continuación se muestra el código correspondiente al LicenciaturaController:

```java
@RestController
@RequestMapping(value = "/licienciatura")
@Log4j2
public class LicenciaturaController {
  

    @GetMapping
    public ResponseEntity<?> getAllLicenciatura() {
       
    }

    @PostMapping
    public Licenciatura createLicenciatura(@RequestBody Licenciatura licenciatura) {
        
    }

    @PutMapping("/{revoe}")
    public ResponseEntity<?> updateLicenciatura(@RequestBody Licenciatura licenciatura, @PathVariable(value = "revoe") String revoe) {
 
    }

    @DeleteMapping("/{id}")
    public void deleteKardex(@PathVariable(value = "id") Long id) {
    }
}
```

## O - Principio de Abierto/Cerrado (Open/Closed Principle)

El principio de abierto/cerrado sostiene que una clase debe estar abierta para su extensión pero cerrada para su modificación. En lugar de modificar el código existente, debemos extender su funcionalidad agregando nuevas clases o módulos. Esto nos permite agregar nuevas características sin afectar el código existente, lo que reduce el riesgo de introducir errores.

Proyecto *coa-api*

Se puede observar que los controladores están diseñados de manera que se puedan agregar nuevas operaciones sin modificar el código existente.

Por ejemplo, si se desea agregar una nueva operación al controlador LicenciaturaController, se puede crear un nuevo método en el controlador sin necesidad de modificar los métodos existentes. Esto se logra gracias al uso de anotaciones como @GetMapping, @PostMapping, @PutMapping y @DeleteMapping, que permiten definir fácilmente nuevas rutas y operaciones HTTP sin modificar el código existente.

A continuación se muestra el código correspondiente al LicenciaturaController:

```java
@RestController
@RequestMapping(value = "/licienciatura")
@Log4j2
public class LicenciaturaController {
   

    @GetMapping
    public ResponseEntity<?> getAllLicenciatura(){}

    @PostMapping
    public Licenciatura createLicenciatura(@RequestBody Licenciatura licenciatura) { }

    @PutMapping("/{revoe}")
    public ResponseEntity<?> updateLicenciatura(@RequestBody Licenciatura licenciatura, @PathVariable(value = "revoe") String revoe) {}

    @DeleteMapping("/{id}")
    public void deleteKardex(@PathVariable(value = "id") Long id) {}
}
```

## L - Principio de Sustitución de Liskov (Liskov Substitution Principle)

El principio de sustitución de Liskov establece que los objetos de una clase base deben poder ser reemplazados por instancias de sus clases derivadas sin alterar la corrección del programa. En otras palabras, si una clase A es un subtipo de una clase B, entonces los objetos de tipo B pueden ser sustituidos por objetos de tipo A sin causar efectos adversos. Este principio promueve la interoperabilidad y la coherencia en el diseño de nuestras clases.

Proyecto *sie-api*

Podemos observar en la clase CostoMateriaService que realiza operaciones relacionadas con el costo de las materias:

```java
@Service
@Log4j2
public class CostoMateriaService {

    @Autowired
    private CostoMateriaRepository costoMateriaRepository;

    public List<CostoMateria> getAllCostoMaterias() throws Exception {
        log.info("getAllCostoMaterias");
        List<CostoMateria> costoMateriaList = costoMateriaRepository.findAll();
        log.info("obtuvo todas las materias");
        if (costoMateriaList.isEmpty()) {
            throw new COAException("No se encontraron datos");
        }

        return costoMateriaList;
    }

    public CostoMateria createCostoMateria(CostoMateria costoMateria) {
        log.info("Se crea costo materia: " + costoMateria.toString());
        return costoMateriaRepository.save(costoMateria);
    }

    public CostoMateria updateCostoMateria(CostoMateria costoMateria) throws Exception {
        Optional<CostoMateria> costoMateriaOptional = costoMateriaRepository.findCostoMateriaById(costoMateria.getMateriaId());

        if (costoMateriaOptional.isPresent()) {
            log.info("Actualizando costo materia: " + costoMateria.toString());
            return costoMateriaRepository.save(costoMateria);
        }

        throw new COAException("No se encuentra el costo de la materia: " + costoMateria.toString());
    }

    public void deleteCostoMateria(Long id) {
        costoMateriaRepository.deleteById(id);
    }
}
```

La clase CostoMateriaService utiliza la dependencia CostoMateriaRepository para acceder y manipular los datos de los costos de las materias.

A continuación, se muestra el código de la interfaz CostoMateriaRepository que extiende de JpaRepository:

```java
public interface CostoMateriaRepository extends JpaRepository<CostoMateria, Long> {
    Optional<CostoMateria> findCostoMateriaById(Long id);
}
```

En este caso, CostoMateriaRepository es una clase derivada de JpaRepository y proporciona una implementación específica para las operaciones relacionadas con los costos de las materias.

Ahora, si observamos el código del servicio CostoMateriaService, podemos ver que se inyecta la dependencia CostoMateriaRepository, que es una instancia de CostoMateriaRepository que implementa los métodos definidos en JpaRepository. Esto significa que se está utilizando una instancia de la clase derivada CostoMateriaRepository en lugar de JpaRepository, y el servicio se basa en los métodos proporcionados por CostoMateriaRepository para realizar las operaciones de persistencia relacionadas con los costos de las materias.

El principio de Sustitución de Liskov se cumple en este caso porque CostoMateriaRepository es una clase derivada que se utiliza en lugar de la clase base JpaRepository sin alterar el comportamiento esperado del sistema. El servicio CostoMateriaService puede trabajar con CostoMateriaRepository de la misma manera que trabajarían con JpaRepository, lo que demuestra que el código cumple con el principio de LSP.

## I - Principio de Segregación de Interfaces (Interface Segregation Principle)

El principio de segregación de interfaces nos dice que los clientes no deben depender de interfaces que no utilizan. En lugar de tener interfaces monolíticas, deberíamos preferir interfaces más pequeñas y específicas. Esto evita que las clases dependan de métodos que no necesitan, reduciendo así la complejidad y el acoplamiento.

Proyecto *sie-api*

La clase PagoService define métodos para realizar operaciones CRUD (Create, Read, Update, Delete) en relación a los pagos. Sin embargo, no encontramos métodos que no sean relevantes para las operaciones de pago. Esto muestra un diseño que cumple con el principio de Segregación de Interfaces.

Para identificar aún más la aplicación del principio ISP en el código, necesitamos examinar la interfaz PagoRepository. Aquí está el código correspondiente:

```java
public interface PagoRepository extends JpaRepository<Pago, Long> {

    Optional<Pago> findByAlumnoIdAndMateriaId(Long AlumnoId, Long MateriaId);
}
```

La interfaz PagoRepository extiende JpaRepository, una interfaz proporcionada por Spring Data JPA que contiene una amplia gama de métodos para realizar operaciones de persistencia. Sin embargo, en la clase PagoService, solo se inyecta y utiliza PagoRepository, sin hacer referencia a JpaRepository directamente. Esto indica una implementación que sigue el principio ISP, ya que la clase PagoService depende únicamente de la interfaz PagoRepository, que proporciona los métodos específicos necesarios para las operaciones de pago.

Cumple con el principio de Segregación de Interfaces (ISP) al asegurarse de que la clase PagoService solo dependa de los métodos relevantes proporcionados por la interfaz PagoRepository, sin requerir que dependa de interfaces o métodos innecesarios de JpaRepository. Esto permite una mayor cohesión y un diseño más modular.

## D - Principio de Inversión de Dependencia (Dependency Inversion Principle)

El principio de inversión de dependencia establece que los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones. Además, las abstracciones no deben depender de los detalles, sino que los detalles deben depender de las abstracciones. Esto nos permite crear sistemas más flexibles y fácilmente intercambiables, ya que podemos cambiar las implementaciones sin afectar el código que las utiliza.

Proyecto *sie-api*

Para las clases PagoService con PagoRepository.

```java
@Service
@Log4j2
public class PagoService {

    @Autowired
    private PagoRepository pagoRepository;

    public List<Pago> getAllPagos() throws Exception {}

    public Pago createPago(Pago pago) {}

    public Pago updatePago(Pago pago) throws Exception {}

    public void deletePago(Long id) {}
}
```

En este caso, PagoService depende de la interfaz PagoRepository, no de una implementación concreta. La dependencia se establece mediante la anotación @Autowired, que permite la inyección de dependencias de manera automática en Spring. Esto cumple con el principio de Inversión de Dependencia, ya que PagoService depende de una abstracción (la interfaz PagoRepository) en lugar de depender directamente de una implementación concreta.
