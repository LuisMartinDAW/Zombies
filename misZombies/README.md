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

`mapping (address => uint) favoriteNumber;`

`function setMyNumber(uint _myNumber) public {
// Actualiza tu mapeo favoriteNumber para guardar _myNumber dentro de msg.sender.
fovoriteNumber[msg.sender] = _myNumber;
//^La sintaxis para guardar datos en un mapeo es como en los arrays
}`

`function whatIsMyNumber() public view returns (uint){
//Conseguimos el valor guardado en la dirección //del emisor. Será 0 si el emisor no ha llamado //a setMyNumber todavia
return favoriteNumber[msg.sender];
}`

En este trivial ejemplo, cualquier puede llamar a **setMyNumber** y guardar un **uint** en nuestro contrato, que estará atado a su dirección. Entonces, cuando llamen a **whatIsMyNumber**, deberia devolverles el **uint** que han guardado.

Usando **msg.sender** te da la seguridad de la blockchain de Ethereum. La única forma de que otra persona edite la información de esta sería robando la clave privada asociada a la dirección Ethereum.

**CAPTÍTULO 4: REQUIRE**

En la lección 1, hicimos que los usuarios pudiesencrear nuevos zombies llamando a **createRandomZombie** e introduciendo un nombre. Sin embargo, si un usuario continuase llamando a esta función crearía un ejército de zombis ilimitando, el juego no sería muy divertido.

Vamos a hacer que un jugador solo pueda llamar a esta función una vez. De esta manera los nuevo jugadores podrán llamar a esta función cuando empiezan el juego para crear el primer zombi de su ejército.

¿Cómo podemos hacer para que esta función solo pueda ser llamada una vez por jugador?.

Para eso usamos **require.require** hace que la función lanze un error y pare de ejecutarse si la condición no es verdadera:

`function sayToVitalik(string _name) public returns(string){
//Compara si _name es igual a "Vitalik". Lanza //un error si no lo son.
//(Nota: Solidity no tiene su propio comparador //de string por lo que compararemos sus hashes //keccak256 para ver si sus strings son iguales)
require(keccak256(_name) == keccak256("Vitalik"));
//Si es verdad, continuamos con la función:
return "Hi!";
}`

Si llamas a la función con **sayToVitalik("Vitalik")**, esta devolverá "Hi!". Si la llamas con cualquier otra entrada, lanzará un error y no se ejecutará.

De este modo **require** es muy útil a la hora de verificar que ciertas condiciones sean verdaderas antes de ejecutar una función.


***CAPÍTULO 5: HERENCIA***

Nuestro código está haciendose un poco largo.Mejor que hacer un contrato extremadamente largo, a veces tiene sentido separar la lógica de nuestro código en múltiples contratos para organizar el código.

Una característica de Solidity que hace más manejable esto es la **herencia** de los contartos:

`contract Doge{
 function catchphrase() public returns (string) {
   return "So Wow CryptoDoge";
 }
}`
`contracto BabyDoge is Doge{
  function anotherCatchphrase() public returns (string){
  return "Such Moon BabyDoge";
  }
}`

**BabyDoge** HEREDA **Doge**. Eso significa que si compila y ejecutas **BabyDoge**, este tendrá acceso tanto a **catchphrase()**  como a **anotherCatchphrase()** ( y a cualquier otra función publica que definamos en **Doge**).

Esto puede usarse como una herencia lógica(como una subclase, un **Gato** es un **Animal**). Pero también puede usarse simplemente para organizar tu código agrupando lógica similar en diferentes clases.

***CAPÍTULO 6: IMPORTAR ***
Nuestro código estaba quedando algo largo, por eso lo hemos dividido en múltiples archivos haciendolo así más manejable. Así es como normalmente deberás guardar tu código base en tus proyectos de Solidity.

Cuando tienes múltiples archvos y quieres importar uno dentro de otro de Solidity usa la palabra clave **import**:

`import "./someothercontract.sol";

contract newContrac is SomeOtherContract{

}`
Entonces si tenemos un fichero llamado somethercontract.sol en el mismo directorio que este contrato (eso es lo que significa ./), será importado por el compilador.

***CAPÍTULO 7: STORAGE VS MEMORY***

En Solidity, hay dos sitios donde puedes guardar variables - en **storage** y en **memory**

STORAGE se refiere a las variables que guardadas permanentemente en la blockchain. La variables de tipo **memory** son temporales, y son borradas en lo que una función externa llama a tu contrato. Piensa que es como un disco duro vs la RAM de tu ordenador.

La mayoría del tiempo no necesitar usar estas palabras clave ya que Solidity las controla por defecto. Las variables e estado ( variables declaradas fuera de las funciones) son pordefecto del tipo **storage** y son guardadas permanentemente en la blockchain, mientras que las variables declaradas dentro de las funciones son por defecto de tipo memory y desaparecerán una vez llamada a la función haya terminado.

Aun así, hay momentos en los que necesitas usar estas palabras clave, concretamente cuando estes trabajando con **structs** y **arrays** dentro de las funciones:
`contract SandwichFactory {

  struct Sandwich{
    string name;
    string status;
  }
  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];

      //^Parece algo sencillo, pero solidity te //dará un warming diciendo que deberías //declararlo `storage`o `memory`.

// De modo que deberias declararlo como storage, asi:
`Sandwich storage mySandwich = sandwiches[_index];`

//...donde myyandwich es un apuntador a sandwiches[_index] de tipo storage, y...
`mySandwich.status = "Eaten!";`
//..modificará la variable temporal y no tendrá efecto. en sandwiches[_index +1]. Pero puedes hacer lo siguiente:
`sandwiches[_index +1] = anotherSandwich;`
//...si queres que la copia con los cambios se guarden en la blockchain.
  }

}`

***CAPÍTULO 8: ADN DEL ZOMBI***

La fórmula para calcular el ADN del nuevo zombi es simple: Es simplemente el promedio entre el ADN del zombi que hemos alimentado y el ADN del zombi objetivo.

Por ejemplo:
`function testDnaSplicing() public {
uint zombieDna = 2222222222222222;
uint targetDna = 4444444444444444;
uint newZombieDna = (zombieDna + targetDna) / 2;
}`

Más tarde haremos nuestra fórmula más complicada si queremos, añadiendole por ejemplo algún valor aleatorio al nuevo ADN. Pero por el momento vamos a dejarlo simple - siempre podemos volver a ello más adelante

***CAPÍTULO 9: MÁS EN LA VISIBILIDAD DE FUNCIONES***
El problema es que hemos intentado llamar a la función **_createZombie** desde **ZombieFeeding**, pero **_createZombie** es una función **private** dentro de **ZombieFactory**. Eso significa que ninguno de los demás contratos que hereden de **ZombieFactory** podrán acceder a ello.

***INTERNAL Y EXTERNAL***

Además de **public** y **private**, Solidity tiene dos tipos de visibilidad más para las funciones: **internal** y **external**.

**internal** es lo mismo que **private**, a exception de que es también accesible desde otros controtatos que hereden de este.

**external** es parecido a **public**, a excepción que estas funciones SOLO puedes ser llamadas desde fuera del contrato. Hablaremos más adelante sobre cuando querrás usar **external** y **public**.

Para declarar las funciones **internal** y **external**, la sintaxis es igual que **private** y **public**:

`contract Sandwich {`
  `uint private sandwichesEaten = 0;`

  `function eat() internal {`
    `sandwichesEaten++;`
  `}`
  `}`

  ´contract BLT is Sandwich {`
    `uint private bancoSandwichesEaten = 0;`

    `function eatWithBacon() public returns (string) {`
      `baconSandwichesEaten++;`
      //Podemos llamar a esta función aquí porque es internal
      `eat();`
    `}`
  `}`

  ***CAPITULO 10***

      ¿QUÉ COMEN LOS ZOMBIS?

      Es hora de alimentar a nuestros zombis

      Para hacer esto necesitamos leer el KittyDna del contrato inteligente CryptoKitties.
      Podemos hacer eso debido a que los datos de los CryptoKitties guardados en la blockchain son públicos.¡¿No es blockchain genial?!.

**INTERACTUANDO CON OTROS CONTRATOS**

Para que nuestro contrato pueda hablar a otro contrato de la blockchain que no poseemos, necesitamos definir una **interfaz**.

Vamos a ver un simple ejemplo. Digamos que hay un contrato en la blockchain tal que asi:

`contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view return (uint){
    return numbers[_myAddress];
  }
}`

Este sería un simple contrato donde cualquiera puede guardar su número de la suerte, y este estará asociado a su dirección de Ethereum. De esta forma cualquera podría ver el número de la suerte de una persona usando su dirección.
Ahora digamos que tenemos un contrato externo que quere leer la información de este contrato usando la función **getNum**.

Primero debemos usar una **interfaz** del contrato **LuckyNumber**:

`contract NumberInterface {`
  `function getNum(address _myAddress) public view returns (uint);`
`}`

Ten en cuenta que esto se asemeja a definir un contrato, con alguna diferencia.

**Primero**, solo declaramos las funciones con las que queremos interactuar - en este caso **getNum** y no mencionamos ninguna otra función o variables de estado.

**Segundo**, no difinimos el cuerpo de la función. En vez de usar las llaves({ y }), solamente terminaremos la función añadiendo un punto y coma al final de la declaración (;).

Sería como definir el esqueleto del contrato. Así es como conoce el compilador a las interfaces.

Incluyendo esta interfaz en el código de tu Dapp nuestro contrato sabe como son las funciones de otro contrato, como llamarlas, y que tipo de respuesta recibimos.

Entraremos en como llamar a las funciones de otros contratos en la siguiente lección, por ahora vamos a declarar nuestra interfaz para el contrato de CryptoKitties.

La función es así:

`function getKitty(uint256 _id) external view returns(`
  bool isGestating,
  bool isReady,
  uint256 cooldownIndex,
  uint256 nextActionAt,
  uint256 siringWithId,
  uint256 birthTime,
  uint256 matronId,
  uint256 sireId,
  uint256 generation,
  uint256 genes

){
  Kitty storage kit = kitties[_id];

  //Si esta variable es 0 entonces no se esta gestando
  isGestating = (kit.siringWithId != 0);
  isReady = (kit.cooldownWndBlock <= block.number);
  cooldownIndex = uint256(kit.cooldownIndex);
  nextActionAt = uint256(kit.cooldownEndBlock);
  siringWithId = uint256(kit.siringWithId);
  birthTime = uint256(kit.birthTime);
  matronId = uint256(kit.matronId);
  sireId = uint256(kit.sireId);
  generation = uint256(kit.generation);
  genes = kit.genes;

}

La función parece algo diferente de las que hemos usado.
Puedes ver que devuelve... una lista de diferentes valores. **En Solidity puedes devolver más de un valor en una función**


***CAPITULO 11***

**USANDO UNA INTERFAZ**

Definimos la interfaz como:

`contract NumberInterface{
  function getNum(address _myAddress) public view returns (uint);`

  Podemos usarla en el contrato de esta manera:

  `contract MyContract {

    `address NumberInterfaceAddress = 0xab38...`
    // La dirección del contrato FavoriteNumber en Ethereum
    `NumberInterface numberContract = NumberInterface(NumberInterfaceAddress)`
    //Ahora numberContract está apuntando al otro contrato
    `function someFunction()public {`
    // Ahora podemos llamar a getNum de ese contrato:
    `uint num = numberContract.getNum(msg.sender);
    // ...y haz algo con num aqui
    `}`

  `}`

  De esta manera, tu contrato puede interactuar con otro contrato de la blockchain de Ethereum, siempre y cuando la función esté definida como **public** o **external**.

  **CAPITULO 12**

  ***Manejado M´´ultiples Valores Devueltos***

  Esta función **getKitty** es el primero ejemplo que vamos a ver que devuelva múlti`les valores. Vamos a ver como manejarlos:

function multipleReturns() internal returns(uint a, uint b, uint c) {

      returns(1, 2, 3);
    
  }

  `function processMultipleReturns() external {`

      ùint a;`
    `uint b;`
    `uint c;`
    // Así es como hacemos múltiples asignaciones:
    `(a, b, c) = multipleReturns();`
  `}`

  // O si solo nos importa el último de estos valores
  `function getLastReturnValue() external{
  `uint c;`
  // Podemos dejar el resto de campos en blanco:
  `(,,c) = multipleReturns();`
  
  `}`
  
**Es la hora de interactuar con el contrato de CryptoKitties.**

Vamos a hacer una función que recoja los genes del gato del contrato:

1.- Haz una función llamada **feedOnKitty**, y deberá se una función **public**

2-. La función deberá declarar un **uint** llamado **KittyDna**.

Nota: En nuestro **KittyInterface, genes es un **uint256** pero si recuerdas en la lección 1 es un alias **uint256** son la misma cosa

3-. La función tendrá entonces que llamar a la función **KittyContract.getKitty** con **_kittyId** y guardar **genes** en **kittyDna**. Recuerda **getKitty** devuelve un montón de variables(10 para ser exactos.)Pero el único que nos interesa es el último de ellos, **genes** ¡Cuenta las comas con cuidado!.

4-. Finalmente, la función deberá llamar a **feedAndMultiply**, y pasarle tanto **_zombieId** como **kittyDna**.

***BONUS: GENES DE GATO***

La lógicade nuestra función está ahora completa...pro añadamos una característica extra.

Vamos hacer que loss zombis creados a partir de gatos tengan una única caraterística que muestre que son gato-zombis.

Para hacer esto, debemos añadr algo de código del gato en el ADN del zombi.

Estamos solo usando los primeros 12 dígitos que determnan el ADN de la apariencia de un zombi. Así que vamos a usar los últimos 2 dígitos para manejar esas caraterísticas "especiales".

**Sentencias IF**

Una sentencia if en Solidity es igual que en javascript.

function eatBLT(string sandwich) public{
// Recuerda que con strin, debemos comparar sus hashes
}