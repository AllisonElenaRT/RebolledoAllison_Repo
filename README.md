# RebolledoAllison_Repo

![Assembly](https://img.shields.io/badge/Assembly-x86-blue)
![License](https://img.shields.io/badge/License-Academic-green)

## 📋 Descripción

Este repositorio corresponde al Producto Académico 2 del curso Arquitectura del Computador. Contiene ejercicios prácticos que traducen estructuras de control escritas en seudocódigo al lenguaje ensamblador x86.

---

## 📚 Contenido

- [Ejercicio 1: Estructura IF-THEN-ELSE](#ejercicio-1-estructura-if-then-else)
- [Ejercicio 2: Bucle WHILE-DO (Fibonacci)](#ejercicio-2-bucle-while-do-fibonacci)
- [Ejercicio 3: Algoritmo de Euclides (MCD)](#ejercicio-3-algoritmo-de-euclides-mcd)
- [Ejercicio 4: Programa con WHILE e IF-SWAP](#ejercicio-4-programa-con-while-e-if-swap)

---

## 🔧 Ejercicios Desarrollados

### Ejercicio 1: Estructura IF-THEN-ELSE

**Objetivo:** Traducir una estructura condicional IF-THEN-ELSE a lenguaje ensamblador.

**Pseudocódigo:**
```c
if (x >= y) {
    x = x + 2;
    y = y + 2;
} else {
    x = x - 2;
    y = y - 2;
}
```

**Solución en Ensamblador:**
```asm
.model small
.stack
.data
    msg db "Estructura IF-THEN-ELSE"
.code

main PROC
    ; Valores iniciales
    mov ax, 5       ; x = 5
    mov bx, 3       ; y = 3

    ; Comparar x con y
    cmp ax, bx
    jl BLOQUE_ELSE      ; Si x < y, salta a BLOQUE_ELSE


; THEN: x >= y

    add ax, 2       ; x = x + 2
    add bx, 2       ; y = y + 2
    jmp FIN_IF       ; Salta al final del IF


; ELSE: x < y

BLOQUE_ELSE:
    sub ax, 2       ; x = x - 2
    sub bx, 2       ; y = y - 2


; Fin del programa

FIN_IF:
    .exit
main ENDP
end main

```

---

### Ejercicio 2: Bucle WHILE-DO (Fibonacci)

**Objetivo:** Implementar un bucle WHILE para calcular la secuencia de Fibonacci.

**Pseudocódigo:**
```c
n = 5; 
fant = 1; 
f = 1; 
i = 2; 
while (i <= n) { 
    faux = f; 
    f = f + fant; 
    fant = faux; 
    i = i + 1;
}
```

**Solución en Ensamblador:**
```asm
.model small
.stack
.data
n db 5          ; limite del bucle
fant db 1       ; valor anterior
f db 1          ; valor actual
i db 1          ; contador (inicializado en 1)
faux db ?       ; variable temporal

.code
main proc
    mov ax, @data
    mov ds, ax
    
    ; --- WHILE (i <= n) ---
while_start:
    mov al, i       ; cargar i en al
    cmp al, n       ; comparar i con n
    ja fin_while    ; si i > n, salir del bucle
    
    ; faux = f
    mov al, f
    mov faux, al
    
    ; f = f + fant
    mov al, f
    add al, fant
    mov f, al
    
    ; fant = faux
    mov al, faux
    mov fant, al
    
    ; i = i + 1
    mov al, i       ; cargar i
    inc al          ; incrementar
    mov i, al       ; guardar de vuelta
    
    jmp while_start ; volver a comprobar
    
fin_while:
    mov ah, 4Ch     ; finalizar el programa
    int 21h
    
main endp
end main
```

---

### Ejercicio 3: Algoritmo de Euclides (MCD)

**Objetivo:** Calcular el máximo común divisor de dos números a y b según el algoritmo de restas de Euclides.

**Pseudocódigo:**
```c
int a = 5, b = 15, mcd; 
while (a != b) { 
    if (a > b)
        a = a - b;
    else     
        b = b - a; 
} 
mcd = a;
```

**Solución en Ensamblador:**
```asm
.model small
.stack
.data
a db 5        ; primer numero
b db 15       ; segundo numero
mcd db ?      ; resultado del maximo comun divisor

.code
main proc
    mov ax, @data
    mov ds, ax

; --- WHILE (a != b) ---
while_mcd:
    mov al, a
    cmp al, b
    je fin_while        ; si a = b, salir del bucle

    ja a_mayor          ; si a > b → ir a A_MAYOR

    ; --- ELSE: a < b ---
    mov al, b
    sub al, a           ; b = b - a
    mov b, al
    jmp while_mcd       ; volver a comprobar la condicion

; --- IF (a > b) ---
a_mayor:
    mov al, a
    sub al, b           ; a = a - b
    mov a, al
    jmp while_mcd       ; volver al inicio del while

; --- FIN DEL BUCLE ---
fin_while:
    mov al, a
    mov mcd, al         ; mcd = a

    mov ah, 4Ch         ; terminar el programa
    int 21h
main endp
end main
```

---

### Ejercicio 4: Programa con WHILE e IF-SWAP

**Objetivo:** Traduce el siguiente programa escrito en un lenguaje de alto nivel a lenguaje ensamblador. La
órden swap(a, b) intercambia los valores de las variables a y b.


**Pseudocódigo:**
```c
int a = 13, b = 16;
while (a > 10) {
    a = a - 1;
    b = b + 2;
}
if (a < b)  
    swap(a, b);
else
    b = a - 1;
```

**Solución en Ensamblador:**
```asm
.model small
.stack
.data
a db 13        ; valor inicial de a
b db 16        ; valor inicial de b
temp db ?      ; variable auxiliar para el swap

.code
main proc
    mov ax, @data
    mov ds, ax

; --- WHILE (a > 10) ---
while_start:
    mov al, a
    cmp al, 10
    jle fin_while       ; si a <= 10 → salir del bucle

    dec a               ; a = a - 1
    mov al, b
    add al, 2           ; b = b + 2
    mov b, al
    jmp while_start

fin_while:

; --- IF (a < b) ---
    mov al, a
    cmp al, b
    jge else_part       ; si a >= b → ir al ELSE

    ; --- swap(a, b) ---
    mov al, a
    mov temp, al
    mov al, b
    mov a, al
    mov al, temp
    mov b, al
    jmp fin_if

else_part:
    mov al, a
    dec al
    mov b, al           ; b = a - 1

fin_if:
    mov ah, 4Ch         ; terminar el programa
    int 21h
main endp
end main
```

---

## 🛠️ Tecnologías Utilizadas

- **Lenguaje:** Assembly x86 (MASM/TASM)
- **Modelo:** Small Memory Model
- **Interrupciones:** DOS INT 21h

---

## 📖 Conceptos Clave

- Estructuras de control en ensamblador
- Instrucciones de comparación (`cmp`)
- Saltos condicionales (`jl`, `je`, `ja`, `jge`)
- Manipulación de registros
- Gestión de memoria y segmentos de datos

---

## 👨‍💻 Autor

**Rebolledo Tello Allison Elena** ,
- Estudiante de Ingeniería de Sistemas e Informática
- Universidad Continental

---

## 📄 Licencia

Este proyecto es de uso académico para el curso de Arquitectura del Computador.

---

