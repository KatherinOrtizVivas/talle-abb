# talle-abb
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <limits> // Para numeric_limits

using namespace std;

// Definición de la estructura para un nodo del árbol
struct Pelicula {
    string nombre;
    int anio;
    string genero;
    float recaudacion;
    Pelicula *izquierda;
    Pelicula *derecha;

    Pelicula(string n, int a, string g, float r) : nombre(n), anio(a), genero(g), recaudacion(r), izquierda(nullptr), derecha(nullptr) {}
};

// Función para crear un nuevo nodo de película
Pelicula* crearNodo(string nombre, int anio, string genero, float recaudacion) {
    return new Pelicula(nombre, anio, genero, recaudacion);
}

// Función para insertar un nodo en el árbol binario de búsqueda
Pelicula* insertar(Pelicula* raiz, string nombre, int anio, string genero, float recaudacion) {
    if (raiz == nullptr) {
        return crearNodo(nombre, anio, genero, recaudacion);
    }

    if (anio < raiz->anio || (anio == raiz->anio && nombre < raiz->nombre)) {
        raiz->izquierda = insertar(raiz->izquierda, nombre, anio, genero, recaudacion);
    } else {
        raiz->derecha = insertar(raiz->derecha, nombre, anio, genero, recaudacion);
    }
    return raiz;
}

// Función para recorrer el árbol en Inorden (izquierda - raíz - derecha)
void inorden(Pelicula* raiz) {
    if (raiz != nullptr) {
        inorden(raiz->izquierda);
        cout << "Nombre: " << raiz->nombre << ", Año: " << raiz->anio << ", Género: " << raiz->genero << ", Recaudación: " << fixed << raiz->recaudacion << " millones" << endl;
        inorden(raiz->derecha);
    }
}

// Función para recorrer el árbol en Preorden (raíz - izquierda - derecha)
void preorden(Pelicula* raiz) {
    if (raiz != nullptr) {
        cout << "Nombre: " << raiz->nombre << ", Año: " << raiz->anio << ", Género: " << raiz->genero << ", Recaudación: " << fixed << raiz->recaudacion << " millones" << endl;
        preorden(raiz->izquierda);
        preorden(raiz->derecha);
    }
}

// Función para recorrer el árbol en Posorden (izquierda - derecha - raíz)
void posorden(Pelicula* raiz) {
    if (raiz != nullptr) {
        posorden(raiz->izquierda);
        posorden(raiz->derecha);
        cout << "Nombre: " << raiz->nombre << ", Año: " << raiz->anio << ", Género: " << raiz->genero << ", Recaudación: " << fixed << raiz->recaudacion << " millones" << endl;
    }
}

// Función para buscar una película por su nombre
void buscarPelicula(Pelicula* raiz, string nombreBuscar) {
    if (raiz == nullptr) {
        cout << "Película '" << nombreBuscar << "' no encontrada." << endl;
        return;
    }

    if (nombreBuscar == raiz->nombre) {
        cout << "Película encontrada:" << endl;
        cout << "Nombre: " << raiz->nombre << ", Año: " << raiz->anio << ", Género: " << raiz->genero << ", Recaudación: " << fixed << raiz->recaudacion << " millones" << endl;
    } else if (nombreBuscar < raiz->nombre) {
        buscarPelicula(raiz->izquierda, nombreBuscar);
    } else {
        buscarPelicula(raiz->derecha, nombreBuscar);
    }
}

// Función para mostrar todas las películas de un determinado género
void mostrarPeliculasPorGenero(Pelicula* raiz, string generoBuscar) {
    if (raiz != nullptr) {
        mostrarPeliculasPorGenero(raiz->izquierda, generoBuscar);
        if (raiz->genero == generoBuscar) {
            cout << "Nombre: " << raiz->nombre << ", Año: " << raiz->anio << ", Género: " << raiz->genero << ", Recaudación: " << fixed << raiz->recaudacion << " millones" << endl;
        }
        mostrarPeliculasPorGenero(raiz->derecha, generoBuscar);
    }
}

// Función auxiliar para contar el número total de nodos
void contarNodos(Pelicula* nodo, int& count) {
    if (nodo != nullptr) {
        contarNodos(nodo->izquierda, count);
        count++;
        contarNodos(nodo->derecha, count);
    }
}

// Función de comparación para ordenar películas por recaudación (ascendente)
bool compararRecaudacion(const Pelicula* a, const Pelicula* b) {
    return a->recaudacion < b->recaudacion;
}

// Función para recolectar todas las películas en un vector
void recolectarPeliculas(Pelicula* raiz, vector<Pelicula*>& peliculas) {
    if (raiz != nullptr) {
        recolectarPeliculas(raiz->izquierda, peliculas);
        peliculas.push_back(raiz);
        recolectarPeliculas(raiz->derecha, peliculas);
    }
}

// Función para mostrar los 3 fracasos taquilleros
void mostrarFracasosTaquilleros(Pelicula* raiz) {
    if (raiz == nullptr) {
        cout << "No hay películas en el sistema." << endl;
        return;
    }

    int totalPeliculas = 0;
    contarNodos(raiz, totalPeliculas);

    if (totalPeliculas < 3) {
        cout << "No hay suficientes películas para mostrar los 3 fracasos taquilleros." << endl;
        return;
    }

    vector<Pelicula*> peliculas;
    recolectarPeliculas(raiz, peliculas);

    sort(peliculas.begin(), peliculas.end(), compararRecaudacion);

    cout << "\nLos 3 fracasos taquilleros son:" << endl;
    for (int i = 0; i < min((size_t)3, peliculas.size()); ++i) {
        cout << i + 1 << ". Nombre: " << peliculas[i]->nombre << ", Año: " << peliculas[i]->anio << ", Género: " << peliculas[i]->genero << ", Recaudación: " << fixed << peliculas[i]->recaudacion << " millones" << endl;
    }
}

// Función para liberar la memoria del árbol
void liberarArbol(Pelicula* raiz) {
    if (raiz != nullptr) {
        liberarArbol(raiz->izquierda);
        liberarArbol(raiz->derecha);
        delete raiz;
    }
}

int main() {
    Pelicula* raiz = nullptr;
    int opcion;

    do {
        cout << "\n--- Menú ---" << endl;
        cout << "1. Ingresar nueva película" << endl;
        cout << "2. Mostrar recorrido Inorden" << endl;
        cout << "3. Mostrar recorrido Preorden" << endl;
        cout << "4. Mostrar recorrido Posorden" << endl;
        cout << "5. Buscar película por nombre" << endl;
        cout << "6. Mostrar películas por género" << endl;
        cout << "7. Mostrar los 3 fracasos taquilleros" << endl;
        cout << "0. Salir" << endl;
        cout << "Ingrese su opción: ";
        cin >> opcion;
        cin.ignore(numeric_limits<streamsize>::max(), '\n'); // Limpiar el buffer del newline

        switch (opcion) {
            case 1: {
                string nombre;
                int anio;
                string genero;
                float recaudacion;

                cout << "Ingrese el nombre de la película: ";
                getline(cin, nombre);

                cout << "Ingrese el año de realización: ";
                cin >> anio;
                cin.ignore(numeric_limits<streamsize>::max(), '\n');

                cout << "Ingrese el género de la película: ";
                getline(cin, genero);

                cout << "Ingrese la recaudación en millones de dólares: ";
                cin >> recaudacion;
                cin.ignore(numeric_limits<streamsize>::max(), '\n');

                raiz = insertar(raiz, nombre, anio, genero, recaudacion);
                cout << "Película '" << nombre << "' ingresada correctamente." << endl;
                break;
            }
            case 2:
                cout << "\nRecorrido Inorden:" << endl;
                inorden(raiz);
                break;
            case 3:
                cout << "\nRecorrido Preorden:" << endl;
                preorden(raiz);
                break;
            case 4:
                cout << "\nRecorrido Posorden:" << endl;
                posorden(raiz);
                break;
            case 5: {
                string nombreBuscar;
                cout << "Ingrese el nombre de la película a buscar: ";
                getline(cin, nombreBuscar);
                buscarPelicula(raiz, nombreBuscar);
                break;
            }
            case 6: {
                string generoBuscar;
                cout << "Ingrese el género a buscar: ";
                getline(cin, generoBuscar);
                cout << "\nPelículas del género '" << generoBuscar << "':" << endl;
                mostrarPeliculasPorGenero(raiz, generoBuscar);
                break;
            }
            case 7:
                mostrarFracasosTaquilleros(raiz);
                break;
            case 0:
                cout << "Saliendo del programa." << endl;
                break;
            default:
                cout << "Opción inválida. Por favor, intente de nuevo." << endl;
        }
    } while (opcion != 0);

    // Liberar la memoria del árbol antes de salir
    liberarArbol(raiz);

    return 0;
}
