# Keccak256 y Encasillado de tipo

Ethereum incluye una función hash llamada **keccak256**, que es una versión de **SHA3**.
Una función hash lo que hace es mapear una cadena de carácteres a un número aleatorio hexadecimal de 256-bits. Un pequeño cambio en la cadena de texto producirá un hash completamente distinto.

# Casteo de Variables

`uint8 a = 5;
 uint b = 6;
 // dará un error porque a * b devuelve un uint y no un uint8
 uint8 c = a * b;
 // debemos de forzar la variable b para sea convertida a uint8
 uint8 c = a * uint8(b);

`
En el código de arriba. a * b devuelve un uint, pero estábamos intentando guardarlo como unt8, lo que podría causar problemas. Casteándolo a uint8 funcionará y el compilador nos dará error.

# EVENTOS
Los eventos son la la forma en que nuestro contrato comunica que algo sucedió en la cadena de bloques a la interfaz de usuario, el cual puede estar 'escuchando' ciertos eventos y hacer algo cuando sucedan.

` // Declara el evento
  event IntegersAdded(uint x, uint y, uint result);

  function add(uint _x, unt _y) public {
    uint result = _x + _y;
    // Lanza el vento para hacer saber a tu aplicación que la función ha sido llamada:
    emit IntegersAdded(_x, _y, result);
    return result;
  
  }
  
`
La aplicación con la interfaz de usuario podría entonces estar escuchando el evento. Una implementación en JavaScript seria asi:

` YourContarct.IntergersAdded(function(error, result) {
    // hacer algo con result
  }
`

Con arrow function seria así:

`YourContract.IntergersAdded( (error, result) => {

});
`

# Web3.js

Ethereum tiene una librería Javascript

`//Así es como accederemos a nuestro contrato:`

`var abi = /* abi generado por el compilador */`

`var ZombieFactoryContract = web3.eth.contract(abi);`

`var contractAddress = /* Nuestra dirección del contrato en Ethereum después de implementarlo*/`

`/* ZombieFactory ha accedido a las funciones y eventos públicos de nuestro contrato*/`

`var ZombieFactory= ZombieFactoryContract.at(contractAddress);`

`// Algunos eventos están escuchando para recoger el valor del texto:`

`$("#ourButton").click(function(e){
    var name = $("#nameInput").val();`

`//Llama a la función createRandomZombie de nuestro contrato:`
    `ZombieFactory.createRandomZombie(name);
});`

// Escucha al evento de NewZombie, y actualiza la interfaz
var event = ZombieFactory.NewZombie(function(error, result)){

    if(error) return
    generateZombie(result.zombieId, result.name, result.dna);
})

//Recogemos el adn del zombi y actualizamos nuestra imagen
function generateZombie(id, name, dna){

    let dnaStr = String(dna);
    // Rellenamos el ADN con ceros si es menor de 16 caracteres
    while (dnaStr.length < 16)
      snaStr = "0" + dnaStr;

      let zombieDetails = {

        // Los primeros 2 dígitos hacen la cabeza. Tenemod 7 //posibles cabezas, entonces hacemos % 7 para obtener un número //entre 0-6, despues le sumamos 1 para hacerlo entre 1 - 7. Tenemos //7 imágenes llamadas desde "head1.png" hasta "head7.png" que //cargamos en base a este número:
        
  headChoice: dnaStr.substring(0, 2) % 7 + 1,

  // Los siguientes 2 dígitos se refieren a los ojos, 11 variaciones:

  eyeChoice: dnaStr.substring(2, 4) % 11 + 1,

  // 6 variaciones de camisetas:

  shirtChoice: dnaStr.substring(4, 6) % 6 + 1,

  //Los últimos 6 digitos controlas el color.Actualiza el filtro 
  //CSS: hue-rotate que tiene 360 grados:

  skinColorChoice: parseInt(dnaStr.substring(6, 8) / 100 * 360),
  
  eyeColorChoice: parseInt(dnaStr.substring(8, 10) / 100 * 360),
  
  clothesColorChoice: parseInt(dnaStr.substring(10, 12) / 100 * 360),
  
  zombieName: name,
  
  zombieDescription: "A level 1 CryptoZombie",
  
      }
      return zombieDetails
}`

# LECCION 2

***CAPITULO 1:***

En la lección 1, creamos una función querecibía un nombre, lo usaba para generar un zombi aleatorio, y lo añadia a la base de datos de zombis de nuestra app guardada en la blockchain.

En la lección 2, vamos hacer nuestra app más parecida a un juego: Vamos a hacerlo multijugador, y también añadiremos más diversión a la creación de zombis en vez de crearlos aleatoriamente.

¿Cómo crearemos nuevos zombis? ¡ Haciendo que nuestros zombis se "alimenten" de otras formas de vidas.

**Alimentando a los Zombis**

Cuando un zombi se alimenta, infecta al huésped con un virus. El virus convierte al huésped en un nuevo zombi que se une a tu ejército. El nuevo ADN del zombi estará calculado del ADN del zombi original y del ADN del huésped.

***CAPITULO 2: MAPEOS Y DIRECCIONES***

Vamos a hcer nuestro juego multijugador dandoles a los zombis un dueño en la base de datos

Para esto, vamos a necesitar dos nuevos tipos de datos: ***mapping*** y ***address***

***DIRECCIONES***

La blockchain de Ethereum está creada por **cuentas**, que podrían ser como cuentas bancarias. Una cuaenta tiene un balance de **Ether** (la divisa utilizada en la blockchain de Ethereum ), y puedes recibir pagos en Ether de otras cuentas, de la misma manera que tu cuenta bancaria puede hacer transferencias a otras cuentas bancarias.

Cada cuenta tiene una dirección (**address**), que sería como el número de la cuenta bancaria. Es un identificador único que apuntado a una cuenta, y se asemejaría a algo así:
**0x0cE446255506E92DF41614C46F1d6df9Cc969183**

Vamos a entrar en el meollo de las direcciones en otra lección por ahora solo entender que **una dirección está asociada a un usuario específico** ( o un contrato inteligente).

Así que podemos utilizarlo como identificador único para nuestros zombis. Cuando un usuario crea un nuevo zombi interectuando con nuestra app, adjudicaremos la propiedad de esos zombis a la dirección de Ethereum que ha llamado a la función.

***MAPEANDO***

En la Lección 1 vimos los **structs** y los **arrays**. Los **mapeos** son otra forma de organizar los datos en Solidity.

Definr un **mapping** se asemajaría a esto:

`//Para una aplicación financiera, guardamos un uint con el balance de su cuenta:`
`mapping (address => uint) public accountBalance;`

`// O podría usarse para guardar / ver los usuarios basados en un userId`
`mapping(uint => string) userIdToName;`

Un mapeo es esencialmente una asociación **valor-clave** para guardar y ver datos. En el primer ejemplo, la clave es un ***address*** (dirección) y el valor correspondería a un **uint**, y en el segundo ejemplo laclave es un **uint** y el valor un **string**.

***CAPITULO 3: Msg.sender***

Ahora que tenemos nuestros mapeos para seguir el rastro del porpietario de un zombi, queremos actualizar el método **_createZombie** para que los utilice.

Para poder hacer esto, necesitamos algo llamado: **msg.sender**

***msg.sender***

En Solidity, hay una serie de variables globales que están disponbles para todas las funciones. Una de estas es **msg.sender**, que hace referencia a la **dirección** de la persona ( o el contrato inteligente) que ha llamado a esa función.

NOTA: En SOlidity, la ejecución de una función necesita empezar con una llamada exterior. Un contrato se sentará en la blockchain sin hacer nada esperado a que alguien llame a una de sus funciones. Así que siempre habrá un **msg.sender**.

Aquí tenemos un ejemplo de como usar **msg.sender** y actualizar un **mapping**