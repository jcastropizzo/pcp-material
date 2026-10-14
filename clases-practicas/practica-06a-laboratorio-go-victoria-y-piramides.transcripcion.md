# practica-06a-laboratorio-go-victoria-y-piramides — transcripción del paquete

Fuente: [practica-06a-laboratorio-go-victoria-y-piramides.zip](practica-06a-laboratorio-go-victoria-y-piramides.zip).

## Archivos de texto del paquete original

Se incluyen los archivos de código, configuración e instrucciones; los archivos comprimidos anidados se abrieron para recuperar su contenido. No se ejecutaron scripts de instalación. Las plantillas incompletas y la solución provista se conservan tal cual, no se presentan como soluciones elaboradas ni verificadas.

### `Piramide Parte 1/Parte 1/go.mod`

````text
module pcp

go 1.26.8
````

### `Piramide Parte 1/Parte 1/main.go`

````go
package main

import "fmt"

type Pago struct {
	monto        int
	nombreOrigen string
}

type Asociado struct {
	nombre            string
	yuanes            int
}

func NuevoAsociado(nombre string, pagosJefe chan<- Pago, comisionesJefe <-chan int) *Asociado {
	return &Asociado{
		nombre:            nombre,
		yuanes:            1000,
	}
}

func main() {
	// TODO
}


func (A *Asociado) invitar(canalNombres chan string) {
	// TODO
}
````

### `Piramide Parte 1/Parte 1/nombres.go`

````go
package main

import "fmt"

// Go no permite const para slices; var a nivel de paquete es lo más cercano.
var nombres = []string{
	"Guido Ezequiel",
	"Valentin",
	"Martin Alejandro",
	"Pedro Lautaro",
	"Sofía Melina",
	"Ezequiel Ilán",
	"Juan Cruz",
	"Ivan Gonzalo",
	"Carlos Federico",
	"Ignacio Agustin",
	"Leonel",
	"Guido",
	"Mariano",
	"Ivo Martin",
	"Julián Agustín",
	"Julieta",
	"Martín",
	"Lisandro",
	"Guillermo Francisco",
	"Martín León",
	"Juan",
	"Samuel",
	"Matias",
	"Thiago Matias",
	"Tiago Martin",
	"Ezequiel David",
	"Tomas Agustin",
	"Magali",
	"Federico Uriel",
	"Mario Alejandro",
	"Mercedes",
	"Joaquín",
	"Ulises",
	"Nicolás Javier",
	"Gian Agustín",
	"Facundo Nicolás",
	"Juan Cruz",
	"Leif",
	"Damian",
	"Tobias Demián",
	"Juan Cruz",
	"Valentino",
	"Juan",
	"Santiago Ezequiel",
	"Tomas",
	"Ignacio Lucas",
	"Maura",
	"Mauricio",
	"Manuel",
	"Guilherme Heinrich",
	"Segundo",
	"Ramiro Ezequiel",
	"María Valentina",
	"Iván",
	"Ralph Ezequiel",
	"Mateo",
	"Carlos Ignacio",
	"Alexis",
	"Emiliano",
	"Bruno Emilio",
	"Pedro Nicolás",
	"Octavio Valentín",
	"Matías Eliel",
}

func Nombres(canalNombres chan string) {
	// TODO - Parte 1
}
````

### `Piramides Parte 3/Parte 3/go.mod`

````text
module pcp

go 1.26.8
````

### `Piramides Parte 3/Parte 3/main.go`

````go
package main

import (
	"fmt"
	"sync"
)

type Asociado struct {
	nombre string
	yuanes int
}

func NuevoAsociado(nombre string) *Asociado {
	return &Asociado{
		nombre: nombre,
		yuanes: 1000,
	}
}

func main() {
	canalNombres := make(chan string)
	go Nombres(canalNombres)
	var wg sync.WaitGroup
	wg.Add(1)
	raiz := NuevoAsociado("Hernán")
	go raiz.unirseGeneración(canalNombres, &wg)
	wg.Wait()
}

// Hasta acá está resuelta la generación de la pirámide (partes I y II).
func (A *Asociado) unirseGeneración(canalNombres chan string, wg *sync.WaitGroup) {
	defer wg.Done()
	A.invitar(canalNombres, wg)
}

func (A *Asociado) invitar(canalNombres chan string, wg *sync.WaitGroup) {
	// Hacer hasta 4 asociados.
	for numAsociado := 0; numAsociado < 4; numAsociado++ {
		nombreAsociado, ok := <-canalNombres
		if !ok {
			return
		}
		fmt.Println("Asociado: ", nombreAsociado, " con jefe: ", A.nombre)
		asociado := NuevoAsociado(nombreAsociado)
		wg.Add(1)
		go asociado.unirseGeneración(canalNombres, wg)
	}
}
````

### `Piramides Parte 3/Parte 3/nombres.go`

````go
package main

import "fmt"

// Go no permite const para slices; var a nivel de paquete es lo más cercano.
var nombres = []string{
	"Guido Ezequiel",
	"Valentin",
	"Martin Alejandro",
	"Pedro Lautaro",
	"Sofía Melina",
	"Ezequiel Ilán",
	"Juan Cruz",
	"Ivan Gonzalo",
	"Carlos Federico",
	"Ignacio Agustin",
	"Leonel",
	"Guido",
	"Mariano",
	"Ivo Martin",
	"Julián Agustín",
	"Julieta",
	"Martín",
	"Lisandro",
	"Guillermo Francisco",
	"Martín León",
	"Juan",
	"Samuel",
	"Matias",
	"Thiago Matias",
	"Tiago Martin",
	"Ezequiel David",
	"Tomas Agustin",
	"Magali",
	"Federico Uriel",
	"Mario Alejandro",
	"Mercedes",
	"Joaquín",
	"Ulises",
	"Nicolás Javier",
	"Gian Agustín",
	"Facundo Nicolás",
	"Juan Cruz",
	"Leif",
	"Damian",
	"Tobias Demián",
	"Juan Cruz",
	"Valentino",
	"Juan",
	"Santiago Ezequiel",
	"Tomas",
	"Ignacio Lucas",
	"Maura",
	"Mauricio",
	"Manuel",
	"Guilherme Heinrich",
	"Segundo",
	"Ramiro Ezequiel",
	"María Valentina",
	"Iván",
	"Ralph Ezequiel",
	"Mateo",
	"Carlos Ignacio",
	"Alexis",
	"Emiliano",
	"Bruno Emilio",
	"Pedro Nicolás",
	"Octavio Valentín",
	"Matías Eliel",
}

func Nombres(canalNombres chan string) {
	for i, nombre := range nombres {
		canalNombres <- fmt.Sprintf("(%d) %s", i, nombre)
	}
	close(canalNombres)
}
````

### `Piramides Parte 4/Parte 4/go.mod`

````text
module pcp

go 1.26.8
````

### `Piramides Parte 4/Parte 4/main.go`

````go
package main

import (
	"fmt"
	"sync"
	"time"
)

type Pago struct {
	monto        int
	nombreOrigen string
}

type Asociado struct {
	nombre            string
	yuanes            int
	pagosJefe         chan<- Pago   // Canal para enviar pagos al jefe
	pagosAsociados    <-chan Pago   // Canal para recibir pagos de los asociados
	nombresAsociados  [4]string     // Nombres de los asociados
	// TODO Parte IV: agregar los canales para recibir y repartir comisiones.
}

func NuevoAsociado(nombre string, pagosJefe chan<- Pago) *Asociado {
	return &Asociado{
		nombre:            nombre,
		yuanes:            1000,
		pagosJefe:         pagosJefe,
		nombresAsociados:  [4]string{""},
	}
}

func main() {
	canalNombres := make(chan string)
	go Nombres(canalNombres)
	var wg sync.WaitGroup
	wg.Add(1)
	raiz := NuevoAsociado("Hernán", nil)
	go raiz.unirseGeneración(canalNombres, &wg)
	wg.Wait()
}

func (A *Asociado) nSubordinados() int {
	count := 0
	for _, nombre := range A.nombresAsociados {
		if nombre != "" {
			count++
		}
	}
	return count
}

func (A *Asociado) indiceAsociado(nombre string) int {
	for i, n := range A.nombresAsociados {
		if n == nombre {
			return i
		}
	}
	return -1 // Not found
}

func (A *Asociado) enviarComisionesSubordinados(monto int) {
	// TODO Parte IV: repartir el monto en partes iguales entre los subordinados.
}

func (A *Asociado) unirseGeneración(canalNombres chan string, wg *sync.WaitGroup) {
	defer wg.Done()
	A.invitar(canalNombres, wg)
	ticker := time.NewTicker(100 * time.Millisecond)
	defer ticker.Stop()
	for {
		if A.nSubordinados() == 0 {
			// Si soy el jefe, imprimo mi total de yuanes y salgo
			if A.pagosJefe == nil {
				fmt.Printf("Asociado %s terminó con %d yuanes\n", A.nombre, A.yuanes)
				return
			}
			// Solo pago 100 yuanes en cada intervalo de tiempo, si no tengo asociados a los que pagarles comisiones
			select {
			case <-ticker.C:
				pago := Pago{
					monto:        100,
					nombreOrigen: A.nombre,
				}
				A.yuanes -= pago.monto
				// Si yuanes negativos, no puedo pagarle al jefe. Mando 0 y salgo
				if A.yuanes < 0 {
					pago.monto = 0
					A.pagosJefe <- pago
					return
				}
				A.pagosJefe <- pago
			// TODO Parte IV: esperar y procesar una comisión del jefe.
			}
		} else {
			// Si tengo asociados, recibo pagos de ellos y reenvio al jefe. Salvo que sea jefe. Que me quede el 50% de la comisión y le pase el resto a los asociados
			select {
			case pago := <-A.pagosAsociados:
				// Si recibo pago 0, quiere decir que el asociado termino
				if pago.monto == 0 {
					// Encontrar el índice del asociado que me está pagando
					indice := A.indiceAsociado(pago.nombreOrigen)
					if indice >= 0 {
						A.nombresAsociados[indice] = ""
					}
				} else {
					// Reenviar el pago al jefe si tengo un jefe; si soy el jefe principal, me lo quedo.
					if A.pagosJefe == nil {
						A.yuanes += pago.monto
					} else {
						A.pagosJefe <- pago
					}
				}
			// TODO Parte IV: esperar y procesar una comisión del jefe.
			}
		}
	}
}

func (A *Asociado) invitar(canalNombres chan string, wg *sync.WaitGroup) {
	// Hacer hasta 4 asociados
	pagos := make(chan Pago, 4)
	A.pagosAsociados = pagos
	for numAsociado := 0; numAsociado < 4; numAsociado++ {
		nombreAsociado, ok := <-canalNombres
		if !ok {
			return
		}
		A.nombresAsociados[numAsociado] = nombreAsociado
		fmt.Println("Asociado: ", nombreAsociado, " con jefe: ", A.nombre)
		asociado := NuevoAsociado(nombreAsociado, pagos)
		wg.Add(1)
		go asociado.unirseGeneración(canalNombres, wg)
	}
}
````

### `Piramides Parte 4/Parte 4/nombres.go`

````go
package main

import "fmt"

// Go no permite const para slices; var a nivel de paquete es lo más cercano.
var nombres = []string{
	"Guido Ezequiel",
	"Valentin",
	"Martin Alejandro",
	"Pedro Lautaro",
	"Sofía Melina",
	"Ezequiel Ilán",
	"Juan Cruz",
	"Ivan Gonzalo",
	"Carlos Federico",
	"Ignacio Agustin",
	"Leonel",
	"Guido",
	"Mariano",
	"Ivo Martin",
	"Julián Agustín",
	"Julieta",
	"Martín",
	"Lisandro",
	"Guillermo Francisco",
	"Martín León",
	"Juan",
	"Samuel",
	"Matias",
	"Thiago Matias",
	"Tiago Martin",
	"Ezequiel David",
	"Tomas Agustin",
	"Magali",
	"Federico Uriel",
	"Mario Alejandro",
	"Mercedes",
	"Joaquín",
	"Ulises",
	"Nicolás Javier",
	"Gian Agustín",
	"Facundo Nicolás",
	"Juan Cruz",
	"Leif",
	"Damian",
	"Tobias Demián",
	"Juan Cruz",
	"Valentino",
	"Juan",
	"Santiago Ezequiel",
	"Tomas",
	"Ignacio Lucas",
	"Maura",
	"Mauricio",
	"Manuel",
	"Guilherme Heinrich",
	"Segundo",
	"Ramiro Ezequiel",
	"María Valentina",
	"Iván",
	"Ralph Ezequiel",
	"Mateo",
	"Carlos Ignacio",
	"Alexis",
	"Emiliano",
	"Bruno Emilio",
	"Pedro Nicolás",
	"Octavio Valentín",
	"Matías Eliel",
}

func Nombres(canalNombres chan string) {
	// TODO - Parte 1
	// Pista: como hay nombres repetidos, es recomendable enumerarlos para no tener problemas.
	// Go tiene un Printf casi igual al de C :)
	// fmt.Sprintf("(%d) %s", i, nombre)
	for i, nombre := range nombres {
		canalNombres <- fmt.Sprintf("(%d) %s", i, nombre)
	}
	close(canalNombres)
}
````

### `Piramides Solucion/Solución ejercicio/go.mod`

````text
module pcp

go 1.26.8
````

### `Piramides Solucion/Solución ejercicio/main.go`

````go
package main

import (
	"fmt"
	"sync"
	"time"
)

type Pago struct {
	monto        int
	nombreOrigen string
}

type Asociado struct {
	nombre            string
	yuanes            int
	pagosJefe         chan<- Pago   // Canal para enviar pagos al jefe
	comisionesJefe    <-chan int    // Canal para recibir comisiones del jefe
	pagosAsociados    <-chan Pago   // Canal para recibir pagos de los asociados
	nombresAsociados  [4]string     // Nombres de los asociados
	comisionAsociados [4]chan<- int // Canales para enviar comisiones a los asociados
}

func NuevoAsociado(nombre string, pagosJefe chan<- Pago, comisionesJefe <-chan int) *Asociado {
	return &Asociado{
		nombre:            nombre,
		yuanes:            1000,
		pagosJefe:         pagosJefe,
		comisionesJefe:    comisionesJefe,
		nombresAsociados:  [4]string{""},
		comisionAsociados: [4]chan<- int{nil, nil, nil, nil},
	}
}

func main() {
	canalNombres := make(chan string)
	go Nombres(canalNombres)
	var wg sync.WaitGroup
	wg.Add(1)
	raiz := NuevoAsociado("Hernán", nil, nil)
	go raiz.unirseGeneración(canalNombres, &wg)
	wg.Wait()
}

func (A *Asociado) nSubordinados() int {
	// Non-nil channels count as 1, nil channels count as 0
	count := 0
	for _, ch := range A.comisionAsociados {
		if ch != nil {
			count++
		}
	}
	return count
}

func (A *Asociado) indiceAsociado(nombre string) int {
	for i, n := range A.nombresAsociados {
		if n == nombre {
			return i
		}
	}
	return -1 // Not found
}

func (A *Asociado) enviarComisionesSubordinados(monto int) {
	yuanesPorAsociado := monto / A.nSubordinados()
	for _, ch := range A.comisionAsociados {
		if ch != nil {
			// ¿Qué pasa si esto no corre en una goroutine?
			go func(ch chan<- int) { ch <- yuanesPorAsociado }(ch)
		}
	}
}

func (A *Asociado) unirseGeneración(canalNombres chan string, wg *sync.WaitGroup) {
	defer wg.Done()
	A.invitar(canalNombres, wg)
	ticker := time.NewTicker(100 * time.Millisecond)
	defer ticker.Stop()
	for {
		if A.nSubordinados() == 0 {
			// Si soy el jefe, imprimo mi total de yuanes y salgo
			if A.pagosJefe == nil {
				fmt.Printf("Asociado %s terminó con %d yuanes\n", A.nombre, A.yuanes)
				return
			}
			// Solo pago 100 yuanes en cada intervalo de tiempo, si no tengo asociados a los que pagarles comisiones
			select {
			case <-ticker.C:
				pago := Pago{
					monto:        100,
					nombreOrigen: A.nombre,
				}
				A.yuanes -= pago.monto
				// Si yuanes negativos, no puedo pagarle al jefe. Mando 0 y salgo
				if A.yuanes < 0 {
					pago.monto = 0
					A.pagosJefe <- pago
					return
				}
				A.pagosJefe <- pago
			case comision := <-A.comisionesJefe:
				A.yuanes += comision
			}
		} else {
			// Si tengo asociados, recibo pagos de ellos y reenvio al jefe. Salvo que sea jefe. Que me quede el 50% de la comisión y le pase el resto a los asociados
			select {
			case pago := <-A.pagosAsociados:
				// Si recibo pago 0, quiere decir que el asociado termino
				if pago.monto == 0 {
					// Encontrar el índice del asociado que me está pagando
					indice := A.indiceAsociado(pago.nombreOrigen)
					A.comisionAsociados[indice] = nil
					A.nombresAsociados[indice] = ""
				} else {
					// Reenviar el pago al jefe si tengo un jefe, sino me quedo con el 50% y le paso el resto a los asociados
					if A.pagosJefe == nil {
						A.yuanes += pago.monto / 2
						A.enviarComisionesSubordinados(pago.monto / 2)
					} else {
						A.pagosJefe <- pago
					}
				}
			case comision := <-A.comisionesJefe:
				// Si recibo comisones del jefe, me quedo con el 50% y le paso el resto a los asociados
				A.yuanes += comision / 2
				A.enviarComisionesSubordinados(comision / 2)
			}
		}
	}
}

func (A *Asociado) invitar(canalNombres chan string, wg *sync.WaitGroup) {
	// Hacer hasta 4 asociados
	pagos := make(chan Pago, 4)
	A.pagosAsociados = pagos
	for numAsociado := 0; numAsociado < 4; numAsociado++ {
		nombreAsociado, ok := <-canalNombres
		if !ok {
			return
		}
		A.nombresAsociados[numAsociado] = nombreAsociado
		fmt.Println("Asociado: ", nombreAsociado, " con jefe: ", A.nombre)
		comision := make(chan int, 1)
		A.comisionAsociados[numAsociado] = comision
		asociado := NuevoAsociado(nombreAsociado, pagos, comision)
		wg.Add(1)
		go asociado.unirseGeneración(canalNombres, wg)
	}
}
````

### `Piramides Solucion/Solución ejercicio/nombres.go`

````go
package main

import "fmt"

// Go no permite const para slices; var a nivel de paquete es lo más cercano.
var nombres = []string{
	"Guido Ezequiel",
	"Valentin",
	"Martin Alejandro",
	"Pedro Lautaro",
	"Sofía Melina",
	"Ezequiel Ilán",
	"Juan Cruz",
	"Ivan Gonzalo",
	"Carlos Federico",
	"Ignacio Agustin",
	"Leonel",
	"Guido",
	"Mariano",
	"Ivo Martin",
	"Julián Agustín",
	"Julieta",
	"Martín",
	"Lisandro",
	"Guillermo Francisco",
	"Martín León",
	"Juan",
	"Samuel",
	"Matias",
	"Thiago Matias",
	"Tiago Martin",
	"Ezequiel David",
	"Tomas Agustin",
	"Magali",
	"Federico Uriel",
	"Mario Alejandro",
	"Mercedes",
	"Joaquín",
	"Ulises",
	"Nicolás Javier",
	"Gian Agustín",
	"Facundo Nicolás",
	"Juan Cruz",
	"Leif",
	"Damian",
	"Tobias Demián",
	"Juan Cruz",
	"Valentino",
	"Juan",
	"Santiago Ezequiel",
	"Tomas",
	"Ignacio Lucas",
	"Maura",
	"Mauricio",
	"Manuel",
	"Guilherme Heinrich",
	"Segundo",
	"Ramiro Ezequiel",
	"María Valentina",
	"Iván",
	"Ralph Ezequiel",
	"Mateo",
	"Carlos Ignacio",
	"Alexis",
	"Emiliano",
	"Bruno Emilio",
	"Pedro Nicolás",
	"Octavio Valentín",
	"Matías Eliel",
}

func Nombres(canalNombres chan string) {
	// TODO - Parte 1
	// Pista: como hay nombres repetidos, es recomendable enumerarlos para no tener problemas.
	// Go tiene un Printf casi igual al de C :)
	// fmt.Sprintf("(%d) %s", i, nombre)
	for i, nombre := range nombres {
		canalNombres <- fmt.Sprintf("(%d) %s", i, nombre)
	}
	close(canalNombres)
}
````

### `README.md`

````text
# Ejercicio 3 - Goroutines y channels

Ejecutar con:

```bash
go run .
```

Las cantidades de colaboradores y autos a construir se configuran con las constantes al comienzo de `main.go`.
````

### `ejercicio_victoria_template.go`

````go
package main

import (
	"fmt"
	"math/rand"
	"time"
)
// En este caso usamos el mismo struct para representar pedidos de faros y carrocerias porque no hace
// falta diferenciarlos a fines de este ejercicio.
type Pedido struct {
	colaboradorID int // Se utiliza para el print en el método Victoria
	terminado     chan struct{}
}

type Taller struct {
	faros       chan Pedido
	carrocerias chan Pedido
}

func NuevoTaller() *Taller {

}

func (t *Taller) TraerFaro(colaboradorID int) {


}

func (t *Taller) TraerCarroceria(colaboradorID int) {

	// TODO
}

func armarUnidad() {
	time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
}

func (t *Taller) Victoria(cantidadAutos int) {
	
}
````

### `go.mod`

````text
module ejercicio3

go 1.22
````

### `main.go`

````go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

const (
	CANT_COLABORADORES_FAROS       = 6
	CANT_COLABORADORES_CARROCERIAS = 7
	CANT_AUTOS                     = 15
)

func main() {
	fmt.Printf("Colaboradores de faros: %d\n", CANT_COLABORADORES_FAROS)
	fmt.Printf("Colaboradores de carrocerias: %d\n", CANT_COLABORADORES_CARROCERIAS)
	fmt.Printf("Autos a construir: %d\n\n", CANT_AUTOS)

	taller := NuevoTaller()

	for id := 1; id <= CANT_COLABORADORES_FAROS; id++ {
		go colaboradorFaro(taller, id)
	}

	for id := 1; id <= CANT_COLABORADORES_CARROCERIAS; id++ {
		go colaboradorCarroceria(taller, id)
	}

	taller.Victoria(CANT_AUTOS)
}

func colaboradorFaro(taller *Taller, id int) {
	for {
		taller.TraerFaro(id)
		time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
	}
}

func colaboradorCarroceria(taller *Taller, id int) {
	for {
		taller.TraerCarroceria(id)
		time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
	}
}
````
