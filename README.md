## Ejemplo escritura de archivos binarios y mbr


+ Struct y librerias

```c++
package main

import (
	"bytes"
	"encoding/binary"
	"fmt"
	"log"
	"os"
	"time"
	//"unsafe"
	
)

//Struct ejemplo donde solo guardo el tamanio, pero tenes que meterle toda la info que pide
type mbr2 struct { //3
	Tamanio uint8
	
}

func main() {

    
	writeFile(100)
	fmt.Println("Reading File: ")
	readFile()
}


```

+ Creacion de archivo binario

```c++
func writeFile(tamanio int) {
	//Datos mbr
	
	
	
	//Aqui creas el archivo binario en el path solicitado
	file, err := os.Create("testesss.bin")
	defer file.Close()
	if err != nil {
		log.Fatal(err)
	}


	//con esto escribo la variable byte donde le pongo que sea 0 pero podes guardar lo que querras
	//aunque podes usar int8 como dice Luis perooo tendrias que pasarlo a binario
	var s string = "0"	
	sb := []byte(s)
	
	//Este es el hack, en los parametros del seek te posicionas en el tamanio que queras imaginate quieres 100 bytes entonces el truco es 
	//como te posicionas hasta ahi todo lo que venga antes te lo llena de esos bytes
	file.Seek(99,0)
	//Ya posicionado le mandas sb que son tus bytes (es solo 1)
	file.Write(sb)
	
    //El chiste es que ya que tenes tu disco no te podes pasar de esos 100 bytes
	
	//Entonces por eso es importante los atributos de tamanio 
	//Lo primero que vas a guardar es tu mbr entonces nos posicionamos en el inicio en 0
	file.Seek(0, 0)
	
     //y aqui ya podemos meter el mbr en la posicion 0 sin afectar el tamanio ya que cayo sobre los 100bytes
	//Escritura del mbr
	disco2 := mbr2{}
	disco2.Tamanio = uint8(tamanio)

	s1 := &disco2
	
	var binario2 bytes.Buffer
	
	binary.Write(&binario2, binary.BigEndian, s1)
	
	//este metodo lo convierte en bytes y escribe en el archivo
	writeNextBytes(file, binario2.Bytes())
	

}


```




+ Metodo para convertir a binario y escribir en el archivo
```c++
func writeNextBytes(file *os.File, bytes []byte) {

	_, err := file.Write(bytes)

	if err != nil {
		log.Fatal(err)
	}

}

```

+ Metodos para leer el archivo 
```c++
func readFile() {
        //abrimos el archivo
	file, err := os.Open("testess.bin")
	defer file.Close()
	if err != nil {
		log.Fatal(err)
	}
         //Nos posicionamos en el inicio en este caso porque sabemos que ahi esta el mbr
	file.Seek(0, 0)

	m2 := mbr2{}
	
         // proceso de convertir de binario a datos donde va el diez es como la cantidad de datos que lees por general pones el size del struct y puse 10 por            //chingar
	data := readNextBytes(file, 10)
	buffer := bytes.NewBuffer(data)
        
	//mostramos la data del mbr
	fmt.Println(data)

	err = binary.Read(buffer, binary.BigEndian, &m2)
	if err != nil {
		log.Fatal("binary.Read failed", err)
	}

	fmt.Println(m2)

	



}


func readNextBytes(file *os.File, number int) []byte {
	bytes := make([]byte, number)

	_, err := file.Read(bytes)
	if err != nil {
		log.Fatal(err)
	}

	return bytes
}

```

## Documentacion shida
+ https://golang.org/pkg/os/
+ https://varunpant.com/posts/reading-and-writing-binary-files-in-go-lang/

