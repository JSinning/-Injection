Deplegamos maquina de dockerlabs con

`sudo ./auto_deploy.sh injection.tar`

hacemos un brarido con **nmap** para lista los puertos abiertios usando 

`sudo nmap -p- --open -sS -sCV --min-rate 5000 -n -Pn -vvv 172.17.0.2 -oN scaning.txt`

![Pasted image 20240720143743](https://github.com/user-attachments/assets/43c2c7c3-6174-4e97-ba62-7823e98be575)


Ok lugo de hacer el barido de puertos obsevamos que la maquina tiene abiertos el puerto **22, 80** puertos comunes que ya conecemos. Analisando cada puerto obesrvamos que puerto 22 **SSH** con una version de **OpenSSH  8.9p1** no tan actual pero de igunamente no nos permite usar exploits para enuemar usaurios como lo es el  **Username Enumeration**. y finalmente  el puerto 80 **http** en el cual vemos en que tien un titulo llamdo  **Russoki coaching**  en su sitio web ya que es un **APACHE  2.4.52** vamos una flag cookie que es iniciar sesi es inyteresante asi que vamos al navegador y veamos.

![Pasted image 20240720144726](https://github.com/user-attachments/assets/dae3ea47-8a86-428d-af3f-3af0d6ff34ca)

vemos un login asi que los primero que senos acure es probar credenciales por defecto como **admin admin, admin password, administrator administrator....** pero ninguna de estas nos funcionan. Asi que nos tenomos que volver algo creativos primero usamos gobuster para ver si encontramos nuevas rutas y no funciona solo esta la ruta server-status con 403 de status pero eso no es nada interesante vemos el usa php como tecnologia asi pude que se este conectado una base de datos asi que probaremos usando una tecnica comun de bypass para logins con injection sql **administrator** y como password usaresmos **administrator' OR 1=1-- -**

![Pasted image 20240720145730](https://github.com/user-attachments/assets/e3cf2c17-f243-4a75-ad68-cb5283c2ae81)

bingo :) --- lo conseguimos esto nos redirgio a una nuva rutas con un mensaje de Bienbenida con usuario llamado Dylan y un password. Recodemos que tenemos el SSH abierto probamos usuario y  contraseña y veamos que pasa.
![Pasted image 20240720150151](https://github.com/user-attachments/assets/b83f0c4a-5b4d-4cc5-8e07-0a4f1c32fdbe)

bien ya estamos dentro como el usuario dylan.

Bien ahora que estamos dentro usamos el comando **sudo -l**  para ver como podrimos escalar privilegios a usuario root pero como no tenemos nada ya que este usario no tiene comado por usar asi que que los proximo que aremos es inverstigar que comando permiso *SUID* asignado a root co el comando **find** 

`find / -perm -4000 -ls 2>/dev/null`  

y encontramos lo siguientes.

![Pasted image 20240720150901](https://github.com/user-attachments/assets/15b2409b-229f-4d9b-8f57-045986b79ed0)

nos centramos del primero */usr/bin/env* asi que vamos a **GTFOBins** y buscamos *env* en forma de SUID
![Pasted image 20240720151149](https://github.com/user-attachments/assets/b8101f4c-1c0b-452d-b196-9fba50a25de6)


 y vemos que como *SUID*  podemos ejecutar `/usr/bin/env /bin/sh -p` para escalar privilegios.
![Pasted image 20240720151424](https://github.com/user-attachments/assets/054441f1-dc9b-46b2-84e4-1c923b6477ad)


  y como vemos ya somos root.
  

`by juandas13` 
