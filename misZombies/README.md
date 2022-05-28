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

// Algunos eventos están escuchando para recoger el valor del texto:

$("#ourButton").click(function(e){
    var name = $("#nameInput").val();

    //Llama a la función createRandomZombie de nuestro contrato:
    ZombieFactory.createRandomZombie(name);
});

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
}


`