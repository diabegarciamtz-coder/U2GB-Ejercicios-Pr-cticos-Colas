# U2GB-Ejercicios-Practicos-Colas


### Atencion a clientes
```
package ejParcticosColas;

/**
 * Este programa simula la atención de clientes en un supermercado durante 1 hora.
 * Se utiliza una cola para representar a los clientes esperando y se abren hasta 4 cajas
 * si se necesita. Se registran estadísticas como el número de clientes atendidos,
 * el tamaño máximo de la fila y el minuto en que se abre una cuarta caja.
 * 
 * @author  Diana Mabel Garcia Martinez
 *          1224100672
 *          diabegarciamtz@gmail.com
 *          02/11/25
 */
import java.util.Random;

// Implementación personalizada de una cola usando lista enlazada
class Cola<T> {
    private Nodo<T> frente;
    private Nodo<T> fin;
    private int tamaño;
    
    private static class Nodo<T> {
        T dato;
        Nodo<T> siguiente;
        
        Nodo(T dato) {
            this.dato = dato;
            this.siguiente = null;
        }
    }
    
    public Cola() {
        this.frente = null;
        this.fin = null;
        this.tamaño = 0;
    }
    
    public void add(T elemento) {
        Nodo<T> nuevoNodo = new Nodo<>(elemento);
        if (isEmpty()) {
            frente = nuevoNodo;
        } else {
            fin.siguiente = nuevoNodo;
        }
        fin = nuevoNodo;
        tamaño++;
    }
    
    public T poll() {
        if (isEmpty()) {
            return null;
        }
        T dato = frente.dato;
        frente = frente.siguiente;
        if (frente == null) {
            fin = null;
        }
        tamaño--;
        return dato;
    }
    
    public T peek() {
        return isEmpty() ? null : frente.dato;
    }
    
    public int size() {
        return tamaño;
    }
    
    public boolean isEmpty() {
        return frente == null;
    }
}

public class AtencionClientes {
    public static void main(String[] args) {
        Cola<Integer> fila = new Cola<>();
        Random random = new Random();
        int clientesAtendidos = 0;
        int maxFila = 0;
        int tiempoCuartaCaja = 0;
        
        // Simular 60 minutos (1 hora)
        for (int minuto = 1; minuto <= 60; minuto++) {
            // Llega cliente cada 2 minutos en promedio
            if (random.nextInt(2) == 0) {
                fila.add(minuto); // Guardamos el minuto de llegada
                System.out.println("Minuto " + minuto + ": Llega cliente");
            }
            
            // Actualizar máximo de fila
            if (fila.size() > maxFila) {
                maxFila = fila.size();
            }
            
            // Abrir cuarta caja si hay más de 5 clientes
            if (fila.size() > 5 && tiempoCuartaCaja == 0) {
                tiempoCuartaCaja = minuto;
                System.out.println("*** Se abre cuarta caja en minuto " + minuto);
            }
            
            // Atender clientes (3 cajas normales + 1 extra si está abierta)
            int cajasActivas = 3 + (tiempoCuartaCaja > 0 ? 1 : 0);
            
            for (int i = 0; i < cajasActivas && !fila.isEmpty(); i++) {
                int llegada = fila.poll();
                int espera = minuto - llegada;
                clientesAtendidos++;
                System.out.println("Cliente atendido. Esperó: " + espera + " min");
            }
        }
        
        // Mostrar estadísticas
        System.out.println("\n=== ESTADÍSTICAS ===");
        System.out.println("Clientes atendidos: " + clientesAtendidos);
        System.out.println("Máximo en fila: " + maxFila);
        System.out.println("Tamaño medio de fila: " + (clientesAtendidos / 60.0));
        System.out.println("Cuarta caja abierta en minuto: " + 
                          (tiempoCuartaCaja > 0 ? tiempoCuartaCaja : "No se abrió"));
    }
}
```

### Comparando colas
```
package ejParcticosColas;

/**
 * Este programa compara dos colas (queues) para verificar si contienen los mismos elementos
 * en el mismo orden. 
 * Se hace una copia temporal de cada cola para no modificar las originales durante la comparación.
 * 
 * @author  Diana Mabel Garcia Martinez
 *          1224100672
 *          diabegarciamtz@gmail.com
 *          02/11/25
 */

public class ComparadorCola {

    /**
     * Compara dos colas para verificar si son iguales en tamaño y contenido.
     * 
     * @param cola1 Primera cola a comparar
     * @param cola2 Segunda cola a comparar
     * @return true si ambas colas tienen los mismos elementos en el mismo orden, false en caso contrario
     */
    public static boolean sonIguales(Cola<Integer> cola1, Cola<Integer> cola2) {
        // Si las colas tienen diferente tamaño, no pueden ser iguales
        if (cola1.size() != cola2.size()) {
            return false;
        }

        // Se crean copias temporales para no modificar las colas originales
        Cola<Integer> temp1 = copiarCola(cola1);
        Cola<Integer> temp2 = copiarCola(cola2);

        // Se comparan elemento por elemento
        while (!temp1.isEmpty()) {
            // Si algún par de elementos no coincide, las colas no son iguales
            if (!temp1.poll().equals(temp2.poll())) {
                return false;
            }
        }

        // Si se recorrieron completamente sin diferencias, son iguales
        return true;
    }
    
    // Método auxiliar para copiar una cola
    private static Cola<Integer> copiarCola(Cola<Integer> original) {
        Cola<Integer> copia = new Cola<>();
        Cola<Integer> temp = new Cola<>();
        
        // Primero copiamos todos los elementos a una cola temporal
        while (!original.isEmpty()) {
            Integer elemento = original.poll();
            temp.add(elemento);
            copia.add(elemento);
        }
        
        // Restauramos la cola original
        while (!temp.isEmpty()) {
            original.add(temp.poll());
        }
        
        return copia;
    }

    public static void main(String[] args) {
        // Se crean dos colas de enteros
        Cola<Integer> colaA = new Cola<>();
        Cola<Integer> colaB = new Cola<>();

        // Se agregan elementos a ambas colas
        colaA.add(10);
        colaA.add(20);
        colaA.add(30);

        colaB.add(10);
        colaB.add(20);
        colaB.add(30);

        // Se muestran las colas y el resultado de la comparación
        System.out.println("Cola A: " + mostrarCola(colaA));
        System.out.println("Cola B: " + mostrarCola(colaB));
        System.out.println("¿Son iguales? " + sonIguales(colaA, colaB));
    }
    
    // Método auxiliar para mostrar el contenido de una cola sin modificarla
    private static String mostrarCola(Cola<Integer> cola) {
        StringBuilder sb = new StringBuilder("[");
        Cola<Integer> temp = copiarCola(cola);
        
        while (!temp.isEmpty()) {
            sb.append(temp.poll());
            if (!temp.isEmpty()) {
                sb.append(", ");
            }
        }
        sb.append("]");
        
        return sb.toString();
    }
}
```

### Mini super
```
package ejParcticosColas;

/**
 * Este programa simula la asignación de clientes a cajas en un supermercado.
 * Cada cliente toma un carrito si hay disponible y se asigna a la caja con menos personas.
 * Si no hay carritos disponibles, el cliente espera en una cola de espera.
 *
 * @author  Diana Mabel Garcia Martinez
 *          1224100672
 *          diabegarciamtz@gmail.com
 *          02/11/25
 */

public class Supermercado {
    public static void main(String[] args) {
        // Número total de carritos disponibles en el supermercado
        int carritos = 25;

        // Se crean tres colas para representar las cajas del supermercado
        Cola<String> caja1 = new Cola<>();
        Cola<String> caja2 = new Cola<>();
        Cola<String> caja3 = new Cola<>();

        // Cola para clientes que no alcanzan carrito y deben esperar
        Cola<String> esperaCarritos = new Cola<>();

        // Lista de clientes que llegan al supermercado
        String[] clientes = {"Ana", "Luis", "Maria", "Carlos", "Sofia"};

        //  Asignación de clientes
        for (String cliente : clientes) {
            if (carritos > 0) {
                carritos--; // Cliente toma un carrito

                // Se asigna a la caja con menos clientes
                if (caja1.size() <= caja2.size() && caja1.size() <= caja3.size()) {
                    caja1.add(cliente);
                    System.out.println(cliente + " va a Caja 1");
                } else if (caja2.size() <= caja3.size()) {
                    caja2.add(cliente);
                    System.out.println(cliente + " va a Caja 2");
                } else {
                    caja3.add(cliente);
                    System.out.println(cliente + " va a Caja 3");
                }
            } else {
                // Si no hay carritos, el cliente espera
                esperaCarritos.add(cliente);
                System.out.println(cliente + " espera carrito");
            }
        }

        //  Mostrar estado final del sistema
        System.out.println("\nCarritos disponibles: " + carritos);
        System.out.println("Caja 1: " + caja1.size() + " clientes");
        System.out.println("Caja 2: " + caja2.size() + " clientes");
        System.out.println("Caja 3: " + caja3.size() + " clientes");
        System.out.println("Esperando carritos: " + esperaCarritos.size());
    }
}
```


```
```
