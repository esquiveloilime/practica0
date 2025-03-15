## Practica 1 

 identificar los elementos fundamentales de los lenguajes de programación: nombres, marcos de activación, bloques de alcance, administración de memoria, expresiones, comandos, control de secuencia como lo es; selección, iteración y recursión, subprogramas, y tipos de datos

 Haremos esto con el codigo en c de la bilitoeca que nos proporciono el maestro mediante GitHub

 A mi me parecio mas facil realizar los comentarios en visual studio code y agregarlo en un nloque de codigo que nos permite utilizar markdown, esto lo vimos en la practica 0, en la primera sesion

 ``` C

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "memory_management.h"

// Variable estática (almacenada en el segmento de datos)
static int static_var = 0;

// Variable global no inicializada (almacenada en el segmento BSS)
int bss_var;

// Enumeración para géneros de libros (tipo de dato definido por el usuario)
typedef enum {
    FICTION,
    NON_FICTION,
    SCIENCE,
    HISTORY,
    FANTASY,
    BIOGRAPHY,
    OTHER
} genre_t;

// Estructura para libros (tipo de dato compuesto)
typedef struct _book {
    int id; // 'id' es un identificador
    char title[100]; // 'title' es un identificador
    char author[100]; // 'author' es un identificador
    int publication_year; // 'publication_year' es un identificador
    genre_t genre; // 'genre' es un identificador
    int quantity; // 'quantity' es un identificador
    struct _book *next; // 'next' es un identificador (puntero a otro libro)
} book_t;

// Estructura para miembros (tipo de dato compuesto)
typedef struct _member {
    int id; // 'id' es un identificador
    char name[100]; // 'name' es un identificador
    int issued_count; // 'issued_count' es un identificador
    int *issued_books; // 'issued_books' es un identificador (puntero a arreglo dinámico)
    struct _member *next; // 'next' es un identificador (puntero a otro miembro)
} member_t;

// Prototipos de funciones (subprogramas)
const char* genreToString(genre_t genre);
void addBook(book_t **library, int* count);
book_t* findBookById(book_t *library, int bookID);
void displayBooksRecursive(book_t *library);
void displayBooks(book_t *library);
void addMember(member_t **members, int *memberCount);
void issueBook(book_t *library, member_t *members);
void returnBook(book_t *library, member_t *members);
void freeLibrary(book_t *library);
void freeMembers(member_t *members);
void saveLibraryToFile(book_t *library, const char *filename);
void loadLibraryFromFile(book_t **library, int *bookCount, const char *filename);
void saveMembersToFile(member_t *members, const char *filename);
void loadMembersFromFile(member_t **members, int *memberCount, const char *filename);
void displayMembers(member_t *members, book_t *library);
void searchMember(member_t *members, book_t *library);

// Función para convertir el género a texto (subprograma)
const char* genreToString(genre_t genre) {
    // Control de secuencia: Selección (switch-case)
    switch (genre) {
        case FICTION: return "Ficcion";
        case NON_FICTION: return "No Ficcion";
        case SCIENCE: return "Ciencia";
        case HISTORY: return "Historia";
        case FANTASY: return "Fantasia";
        case BIOGRAPHY: return "Biografia";
        case OTHER: return "Otro";
        default: return "Desconocido";
    }
}

// Función para agregar un libro (subprograma)
void addBook(book_t **library, int* count) {
    // Administración de memoria: Asignación dinámica en el heap
    book_t *new_book = (book_t *)malloc(sizeof(book_t));
    if (!new_book) {
        printf("Error al asignar memoria para el nuevo libro.\n");
        return;
    }
    incrementHeapAllocations(new_book, sizeof(book_t));

    // Expresiones: Asignación de valores a los campos del libro
    printf("\nIngresa ID del libro: ");
    scanf("%d", &new_book->id);
    printf("Ingresa titulo del libro: ");
    getchar();
    fgets(new_book->title, 100, stdin);
    new_book->title[strcspn(new_book->title, "\n")] = '\0';
    printf("Ingresa nombre del autor: ");
    fgets(new_book->author, 100, stdin);
    new_book->author[strcspn(new_book->author, "\n")] = '\0';
    printf("Ingresa el ano de publicacion: ");
    scanf("%d", &new_book->publication_year);
    printf("Ingresa el genero del libro (0: FICTION, 1: NON_FICTION, 2: SCIENCE, 3: HISTORY, 4: FANTASY, 5: BIOGRAPHY, 6: OTHER): ");
    int genre;
    scanf("%d", &genre);
    new_book->genre = (genre_t)genre;
    printf("Ingresa la cantidad de libros: ");
    scanf("%d", &new_book->quantity);

    // Comandos: Enlazar el nuevo libro a la lista
    new_book->next = *library;
    *library = new_book;
    (*count)++;

    printf("\nEl libro fue agregado exitosamente!\n");
    displayMemoryUsage();
}

// Función para buscar un libro por ID (subprograma)
book_t* findBookById(book_t *library, int bookID) {
    // Control de secuencia: Iteración (while)
    book_t *current = library;
    while (current) {
        if (current->id == bookID) {
            return current; // Expresión: Retorno de un valor
        }
        current = current->next;
    }
    return NULL; // Expresión: Retorno de un valor
}

// Función para mostrar libros de forma recursiva (subprograma)
void displayBooksRecursive(book_t *library) {
    // Control de secuencia: Recursión
    if (!library) return;
    printf("\nID libro: %d\nTitulo: %s\nAutor: %s\nAno de publicacion: %d\nGenero: %s\nCantidad: %d\n",
        library->id, library->title, library->author, library->publication_year, genreToString(library->genre), library->quantity);
    displayBooksRecursive(library->next); // Llamada recursiva
}

// Función para mostrar todos los libros (subprograma)
void displayBooks(book_t *library) {
    // Control de secuencia: Selección (if-else)
    if (!library) {
        printf("\nNo hay libros disponibles.\n");
        return;
    }
    printf("\nLibros disponibles en biblioteca:\n");
    displayBooksRecursive(library); // Llamada a otro subprograma
    displayMemoryUsage();
}

// Función para agregar un miembro (subprograma)
void addMember(member_t **members, int *memberCount) {
    // Administración de memoria: Asignación dinámica en el heap
    member_t *new_member = (member_t *)malloc(sizeof(member_t));
    if (!new_member) {
        printf("Error al asignar memoria para el nuevo miembro.\n");
        return;
    }
    incrementHeapAllocations(new_member, sizeof(member_t));

    // Expresiones: Asignación de valores a los campos del miembro
    printf("\nIngresa el ID del miembro: ");
    scanf("%d", &new_member->id);
    printf("Ingresa el nombre del miembro: ");
    getchar();
    fgets(new_member->name, 100, stdin);
    new_member->name[strcspn(new_member->name, "\n")] = '\0';
    new_member->issued_count = 0;
    new_member->issued_books = NULL;
    new_member->next = *members; // Comando: Enlazar el nuevo miembro a la lista
    *members = new_member;
    (*memberCount)++;

    printf("\nMiembro agregado exitosamente!\n");
    displayMemoryUsage();
}

// Función para prestar un libro (subprograma)
void issueBook(book_t *library, member_t *members) {
    int bookID, memberID;
    printf("\nIngresa el ID del miembro: ");
    scanf("%d", &memberID);
    printf("Ingresa el ID del libro: ");
    scanf("%d", &bookID);

    // Control de secuencia: Iteración (while)
    book_t *bookFound = findBookById(library, bookID);
    member_t *memberFound = NULL;

    member_t *current_member = members;
    while (current_member) {
        if (current_member->id == memberID) {
            memberFound = current_member;
            break;
        }
        current_member = current_member->next;
    }

    // Control de secuencia: Selección (if-else)
    if (bookFound && memberFound && bookFound->quantity > 0) {
        bookFound->quantity--;
        memberFound->issued_count++;
        memberFound->issued_books = realloc(memberFound->issued_books, memberFound->issued_count * sizeof(int)); // Administración de memoria: Reasignación dinámica
        incrementHeapAllocations(memberFound->issued_books, memberFound->issued_count * sizeof(int));
        memberFound->issued_books[memberFound->issued_count - 1] = bookID;
        printf("\nLibro prestado satisfactoriamente!\n");
    } else {
        printf("\nLibro o miembro no encontrados.\n");
    }
    displayMemoryUsage();
}

// Función para devolver un libro (subprograma)
void returnBook(book_t *library, member_t *members) {
    int bookID, memberID;
    printf("\nIngresa el ID del miembro: ");
    scanf("%d", &memberID);
    printf("Ingresa el ID del libro: ");
    scanf("%d", &bookID);

    // Control de secuencia: Iteración (while)
    book_t *bookFound = findBookById(library, bookID);
    member_t *memberFound = NULL;

    member_t *current_member = members;
    while (current_member) {
        if (current_member->id == memberID) {
            memberFound = current_member;
            break;
        }
        current_member = current_member->next;
    }

    // Control de secuencia: Selección (if-else)
    if (bookFound && memberFound) {
        int found = 0;
        for (int i = 0; i < memberFound->issued_count; i++) {
            if (memberFound->issued_books[i] == bookID) {
                found = 1;
                for (int j = i; j < memberFound->issued_count - 1; j++) {
                    memberFound->issued_books[j] = memberFound->issued_books[j + 1];
                }
                memberFound->issued_count--;
                memberFound->issued_books = realloc(memberFound->issued_books, memberFound->issued_count * sizeof(int)); // Administración de memoria: Reasignación dinámica
                incrementHeapAllocations(memberFound->issued_books, memberFound->issued_count * sizeof(int));
                break;
            }
        }
        if (found) {
            bookFound->quantity++;
            printf("\nLibro devuelto satisfactoriamente!\n");
        } else {
            printf("\nEl miembro no tiene este libro prestado.\n");
        }
    } else {
        printf("\nLibro o miembro no encontrados.\n");
    }
    displayMemoryUsage();
}

// Función para liberar la memoria de la biblioteca (subprograma)
void freeLibrary(book_t *library) {
    // Control de secuencia: Iteración (while)
    book_t *current = library;
    while (current) {
        book_t *next = current->next;
        incrementHeapDeallocations(current);
        free(current); // Administración de memoria: Liberación de memoria
        current = next;
    }
    displayMemoryUsage();
}

// Función para liberar la memoria de los miembros (subprograma)
void freeMembers(member_t *members) {
    // Control de secuencia: Iteración (while)
    member_t *current = members;
    while (current) {
        member_t *next = current->next;
        incrementHeapDeallocations(current->issued_books);
        free(current->issued_books); // Administración de memoria: Liberación de memoria
        incrementHeapDeallocations(current);
        free(current); // Administración de memoria: Liberación de memoria
        current = next;
    }
    displayMemoryUsage();
}

// Función para guardar la biblioteca en un archivo (subprograma)
void saveLibraryToFile(book_t *library, const char *filename) {
    FILE *file = fopen(filename, "w");
    if (!file) {
        printf("Error al abrir el archivo para guardar la biblioteca.\n");
        return;
    }

    // Control de secuencia: Iteración (while)
    book_t *current = library;
    while (current) {
        fprintf(file, "%d\n%s\n%s\n%d\n%s\n%d\n", current->id, current->title, current->author, current->publication_year, genreToString(current->genre), current->quantity);
        current = current->next;
    }

    fclose(file);
    printf("Biblioteca guardada exitosamente en %s\n", filename);
    displayMemoryUsage();
}

// Función para cargar la biblioteca desde un archivo (subprograma)
void loadLibraryFromFile(book_t **library, int *bookCount, const char *filename) {
    FILE *file = fopen(filename, "r");
    if (!file) {
        printf("Error al abrir el archivo para cargar la biblioteca.\n");
        return;
    }

    // Control de secuencia: Iteración (while)
    while (!feof(file)) {
        book_t *new_book = (book_t *)malloc(sizeof(book_t)); // Administración de memoria: Asignación dinámica
        if (!new_book) {
            printf("Error al asignar memoria para el nuevo libro.\n");
            fclose(file);
            return;
        }

        fscanf(file, "%d\n", &new_book->id);
        fgets(new_book->title, 100, file);
        new_book->title[strcspn(new_book->title, "\n")] = '\0';
        fgets(new_book->author, 100, file);
        new_book->author[strcspn(new_book->author, "\n")] = '\0';
        fscanf(file, "%d\n", &new_book->publication_year);
        char genre_str[50];
        fgets(genre_str, 50, file);
        genre_str[strcspn(genre_str, "\n")] = '\0';
        if (strcmp(genre_str, "Ficcion") == 0) new_book->genre = FICTION;
        else if (strcmp(genre_str, "No Ficcion") == 0) new_book->genre = NON_FICTION;
        else if (strcmp(genre_str, "Ciencia") == 0) new_book->genre = SCIENCE;
        else if (strcmp(genre_str, "Historia") == 0) new_book->genre = HISTORY;
        else if (strcmp(genre_str, "Fantasia") == 0) new_book->genre = FANTASY;
        else if (strcmp(genre_str, "Biografia") == 0) new_book->genre = BIOGRAPHY;
        else new_book->genre = OTHER;
        fscanf(file, "%d\n", &new_book->quantity);

        new_book->next = *library; // Comando: Enlazar el nuevo libro a la lista
        *library = new_book;
        (*bookCount)++;
        incrementHeapAllocations(new_book, sizeof(book_t));
    }

    fclose(file);
    printf("Biblioteca cargada exitosamente desde %s\n", filename);
    displayMemoryUsage();
}

// Función para guardar los miembros en un archivo (subprograma)
void saveMembersToFile(member_t *members, const char *filename) {
    FILE *file = fopen(filename, "w");
    if (!file) {
        printf("Error al abrir el archivo para guardar los miembros.\n");
        return;
    }

    // Control de secuencia: Iteración (while)
    member_t *current = members;
    while (current) {
        fprintf(file, "%d\n%s\n%d\n", current->id, current->name, current->issued_count);
        for (int i = 0; i < current->issued_count; i++) {
            fprintf(file, "%d\n", current->issued_books[i]);
        }
        current = current->next;
    }

    fclose(file);
    printf("Miembros guardados exitosamente en %s\n", filename);
}

// Función para cargar los miembros desde un archivo (subprograma)
void loadMembersFromFile(member_t **members, int *memberCount, const char *filename) {
    FILE *file = fopen(filename, "r");
    if (!file) {
        printf("Error al abrir el archivo para cargar los miembros.\n");
        return;
    }

    // Control de secuencia: Iteración (while)
    while (!feof(file)) {
        member_t *new_member = (member_t *)malloc(sizeof(member_t)); // Administración de memoria: Asignación dinámica
        if (!new_member) {
            printf("Error al asignar memoria para el nuevo miembro.\n");
            fclose(file);
            return;
        }

        fscanf(file, "%d\n", &new_member->id);
        fgets(new_member->name, 100, file);
        new_member->name[strcspn(new_member->name, "\n")] = '\0';
        fscanf(file, "%d\n", &new_member->issued_count);
        new_member->issued_books = (int *)malloc(new_member->issued_count * sizeof(int)); // Administración de memoria: Asignación dinámica
        for (int i = 0; i < new_member->issued_count; i++) {
            fscanf(file, "%d\n", &new_member->issued_books[i]);
        }

        new_member->next = *members; // Comando: Enlazar el nuevo miembro a la lista
        *members = new_member;
        (*memberCount)++;
        incrementHeapAllocations(new_member, sizeof(member_t));
        incrementHeapAllocations(new_member->issued_books, new_member->issued_count * sizeof(int));
    }

    fclose(file);
    printf("Miembros cargados exitosamente desde %s\n", filename);
    displayMemoryUsage();
}

// Función para mostrar todos los miembros (subprograma)
void displayMembers(member_t *members, book_t *library) {
    // Control de secuencia: Selección (if-else)
    if (!members) {
        printf("\nNo hay miembros disponibles.\n");
        return;
    }

    // Control de secuencia: Iteración (while)
    member_t *current = members;
    printf("\nMiembros disponibles en biblioteca:\n");
    while (current) {
        printf("\nID miembro: %d\nNombre: %s\nCantidad de libros prestados: %d\n",
            current->id, current->name, current->issued_count);
        for (int i = 0; i < current->issued_count; i++) {
            book_t *book = findBookById(library, current->issued_books[i]);
            if (book) {
                printf("  Libro ID: %d\n  Titulo: %s\n  Autor: %s\n", book->id, book->title, book->author);
            }
        }
        current = current->next;
    }
    displayMemoryUsage();
}

// Función para buscar un miembro por ID (subprograma)
void searchMember(member_t *members, book_t *library) {
    int memberID;
    printf("\nIngresa el ID del miembro: ");
    scanf("%d", &memberID);

    // Control de secuencia: Iteración (while)
    member_t *current = members;
    while (current) {
        if (current->id == memberID) {
            printf("\nID miembro: %d\nNombre: %s\nCantidad de libros prestados: %d\n",
                current->id, current->name, current->issued_count);
            for (int i = 0; i < current->issued_count; i++) {
                book_t *book = findBookById(library, current->issued_books[i]);
                if (book) {
                    printf("  Libro ID: %d\n  Titulo: %s\n  Autor: %s\n", book->id, book->title, book->author);
                }
            }
            displayMemoryUsage();
            return;
        }
        current = current->next;
    }
    printf("\nMiembro no encontrado.\n");
    displayMemoryUsage();
}

// Función principal (subprograma)
int main() {
    // Variables automáticas (almacenadas en el stack)
    book_t *library = NULL;
    member_t *members = NULL;
    int bookCount = 0, memberCount = 0;
    int choice = 0;

    // Cargar datos desde archivos
    loadLibraryFromFile(&library, &bookCount, "library.txt");
    loadMembersFromFile(&members, &memberCount, "members.txt");

    // Control de secuencia: Iteración (do-while)
    do {
        printf("\nMenu de sistema de manejo de biblioteca\n");
        printf("\t1. Agregar un libro\n");
        printf("\t2. Mostrar libros disponibles\n");
        printf("\t3. Agregar un miembro\n");
        printf("\t4. Prestar libro\n");
        printf("\t5. Devolver libro\n");
        printf("\t6. Mostrar miembros disponibles\n");
        printf("\t7. Buscar miembro\n");
        printf("\t8. Guardar y salir\n");
        printf("Indica tu opcion: ");
        scanf("%d", &choice);

        // Control de secuencia: Selección (switch-case)
        switch (choice) {
            case 1:
                addBook(&library, &bookCount);
                break;
            case 2:
                displayBooks(library);
                break;
            case 3:
                addMember(&members, &memberCount);
                break;
            case 4:
                issueBook(library, members);
                break;
            case 5:
                returnBook(library, members);
                break;
            case 6:
                displayMembers(members, library);
                break;
            case 7:
                searchMember(members, library);
                break;
            case 8:
                saveLibraryToFile(library, "library.txt");
                saveMembersToFile(members, "members.txt");
                printf("Saliendo del programa\n");
                break;
            default:
                printf("Esta no es una opcion valida!!!\n");
                break;
        }
    } while(choice != 8);

    // Liberar memoria de la biblioteca y miembros
    freeLibrary(library);
    freeMembers(members);

    return 0;
}

```

Como vimos en este código, se implementó un sistema de manejo de biblioteca en C, donde se utilizaron los elementos fundamentales de los lenguajes de programación de manera práctica y efectiva. Se trabajó con nombres, marcos de activación, bloques de alcance, administración de memoria (tanto en el stack como en el heap), expresiones, comandos, y control de secuencia (selección, iteración y recursión). Además, se definieron subprogramas (funciones) para modularizar el código y se utilizaron tipos de datos primitivos, compuestos y definidos por el usuario.