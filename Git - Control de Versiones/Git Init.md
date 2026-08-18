
Fusionar mis cambios
con los de mi equipo.

Existe un codigo fuente
- V-Diego
- V-Moises - CalcularArea()
- V-Carlos - CalcularArea_()

Ramas o Branches: Es una copia del codigo fuente que podemos hacer propia para modificar a gusto.

Cuando terminan hacen un Pull Request.



.Crear Repositorio
1. Ir a github - crear cuenta
2. Ir a respositorios 
![[Pasted image 20260811180435.png]]
3. Crear nuevo Repositorio
4. ![[Pasted image 20260811180518.png]]
5. ![[Pasted image 20260811180507.png]]
6. Repo creado es:
   https://github.com/AlexVillegasC/progra1.lab1

   7. Installar Git Local![[Pasted image 20260811181005.png]]
8.  Crear Rama master
9. Abrimos el folder donde esta tu proyecto: C:\Users\alexv\source\repos\Progra1.Lab1


GIT INIT:
Crear un repositorio vacio 

git add .
Agrega todos los archivos del folder para subirlos

... .gitignore

git commit -m "Primer Commit"

// crear rama
git branch -M master

// vincular con rama remota
git remote add origin https://github.com/AlexVillegasC/progra1.lab1

// subir cambios
git push origin HEAD

