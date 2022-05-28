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

`
//Así es como accederemos a nuestro contrato:

var abi = /* abi generado por el compilador */

var ZombieFactoryContract = web3.eth.contract(abi);

var contractAddress = /* Nuestra dirección del contrato en Ethereum después de implementarlo*/

/* ZombieFactory ha accedido a las funciones y eventos públicos de nuestro contrato*/

var ZombieFactory= ZombieFactoryContract.at(contractAddress);
`