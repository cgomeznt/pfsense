Instalar Snort
===================

En el menu de pfsense **SystemPackage + Manager + Installed Packages** y escribimos en search **snort** y pulsamos en el boton **Install**

Veremos que se instala el snort	security	4.1.6_8	

Luego nos vamos a **Services + Snort** y vamos a ir configurando lo siguiente

- Services + Snort + Global Settings 

::
	
	- Enable Snort VRT "x" to enable download of Snort free Registered User or paid Subscriber rules

::
	- En esta opción debemos registrarnos en la pagina de snort "https://www.snort.org/users/sign_up" una vez registrados, nos vamos a la opción **Oinkcodes** y nos lo copiamos.

::

	- Snort Oinkmaster Copiamos el Oinkcodes
	
	- Enable Snort GPLv2 "x" to enable download of Snort GPLv2 Community rules
	
	- Enable ET Open "x" to enable download of Emerging Threats Open rules
	
	- Enable OpenAppID "x" to enable download of Sourcefire OpenAppID Detectors
	
	- Enable AppID Open Text Rules "x" to enable download of the AppID Open Text Rules
	
	- Enable FEODO Tracker Botnet C2 IP Rules "x" to enable download of FEODO Tracker Botnet C2 IP rules
	
	- Update Interval 1 DAY

	- Remove Blocked Hosts Interval 1 HOURS
	
	- Keep Snort Settings After Deinstall "x" to retain Snort settings after package removal.
	
- Services + Snort + Updates

	- Debemos pulsar sobre el boton **Force Update** para que descargue todas las Reglas, va demorar un poco, y cuando culmine podemos ver el LOG y tambien en **Installed Rule Set MD5 Signature** como se actualizo
	
Services + Snort + Alerts

	No hay nada que hacer aquí, en este apartado veremos las alertas de ataques o posibles ataques que detecta el snort gracias a las reglas descargadas.
	
Services + Snort + Blocked

	No se configura nada aquí. Se veran las IP bloqueadas y con la descripción del ataque.

Services + Snort + Pass List

	Son las listas blancas, es decir las IP permitidas.
	
Services + Snort + Suppress

	Para remover reglas.
	
Services + Snort + IP List

	IP Reputacional
	
Services + Snort + SID Mgmt

	Snort will automatically enable/disable/modify text rules upon each update using criteria specified in SID Management Configuration lists

Services + Snort + LOG Mgmt

	Administración del LOG, tamaño maximo y retención.
	
Services + Snort + Sync

	Sincronización de la configuración hacia otro Host.
	
Aunque este es el primero del la lista de Menu, lo dejamos de ultimo porque aquí activaremos las configuraciones de IPS/IDS


Services + Snort + Snort Interfaces +

	Pulsamos en Add
	
	Services + Snort + Snort Interfaces + WAN Settings
	
		Enable "x" Enable interface
		
			General Settings
			
			Interface WAN
			
			Description WAN
			
			Snap Length 1518
		
		Block Settings
		
			Block Offenders "x" Checking this option will automatically block hosts that generate a Snort alert. Default is Not Checked.
		
			IPS Mode Legacy Mode
			
			Kill States "x" Checking this option will kill firewall established states for the blocked IP. Default is checked.
			
			Which IP to Block BOTH
		
		Detection Performance Settings
		
			Search Method AC-BNFA
			
		Choose the Networks Snort Should Inspect and Whitelist
		
			Home Net Default
			
			External Net Default
			
			Pass List Default
			
		Choose a Suppression or Filtering List (Optional)
		
			Alert Suppression and Filtering Default
			
		Save
		
	Services + Snort + Snort Interfaces + WAN Categories
	
		Automatic Flowbit Resolution
		
			Resolve Flowbits "x" If checked, Snort will auto-enable rules required for checked flowbits. Default is Checked.
		
		Snort Subscriber IPS Policy Selection
		
			Use IPS Policy "x" If checked, Snort will use rules from one of three pre-defined IPS policies in the Snort Subscriber rules. Default is Not Checked.
		
			IPS Policy Selection Security
			
		Select the rulesets (Categories) Snort will load at startup
		
			Select All
			
		Save
		
	Services + Snort + Snort Interfaces + WAN Rules
	
		Available Rule Categories
		
			Buscamos y seleccionamos IPS Policy - Security y luego pulsamos Enable All
			
			Buscamos y seleccionamos Auto-Flow bit Rules y luego pulsamos Enable All
		
		Apply
			
		
	Services + Snort + Snort Interfaces + WAN Variables
	
		No tocamos nada.
		
	Services + Snort + Snort Interfaces + WAN Preprocs
	
		No tocamos nada, lo dejamos por default
		
	Services + Snort + Snort Interfaces + WAN IP Rep
	
		No tocamos nada, lo dejamos por default
		
	Services + Snort + Snort Interfaces + WAN Logs
	
		No tocamos nada, lo dejamos por default. Pero al momento de querer ver los LOG solo debemos seleccionar que tipo de evento queremos ver.


Ya cuando todo este guardado regresamos 

Services + Snort + Snort Interfaces

y en Services + Snort + Snort Interfaces, vamos a ver nuestra configuración creada y lista para iniciarla

	Interface	Snort Status	Pattern Match	Blocking Mode	Description	Actions
	WAN (em0)	 Start/Stop	    	AC-BNFA		LEGACY MODE		WAN

Iniciamos el Snort esto demora un tiempo y listo, ya en la WAN esta activo el IPS/IDS



Probamos el funcionamiento del Snort
-----------------------------------------

En una maquina virtual o como guste, pero que tenga una IP que le llegue a la WAN del pfsense, ejecutamos el comando **nmap**
En este ejemplo la IP WAN del pfsense es: **192.168.1.109**

Así responde el pfsense con el snort::

	[root@srv-haproxy ~]# nmap -sT 192.168.1.109
	Starting Nmap 7.70 ( https://nmap.org ) at 2023-08-23 22:38 EDT
	Nmap scan report for 192.168.1.109
	Host is up (-0.088s latency).
	Not shown: 997 filtered ports
	PORT     STATE SERVICE
	22/tcp   open  ssh
	443/tcp  open  https
	3389/tcp open  ms-wbt-server
	MAC Address: 00:0C:29:E4:5D:C2 (VMware)

Se le realiza un ataque y vemos como no termina nunca de responder, y si nos vamos al pfsense + snort en Alerts y tambien en Blocked, veremos el bloqueo de la IP
con la descripcion de un scan del nmap::

	[root@srv-haproxy ~]# nmap -A 192.168.1.109
	Starting Nmap 7.70 ( https://nmap.org ) at 2023-08-23 22:38 EDT

Nos vamos al pfsense al apartado **Services + Snort + Alerts** y veremos que tenemos un registro de alertas. (ver Description)

Nos vamos al pfsense al apartado **Services + Snort + Blocked** y veremos que tenemos una IP bloqueada. (ver Description)


Y aun bloquedo, lanzamos el comando de nmap que si nos habia traido respuesta, veremos como ahora no muestra nada porque estamos bloqueado::

	[root@srv-haproxy ~]# nmap -sT 192.168.1.109
	Starting Nmap 7.70 ( https://nmap.org ) at 2023-08-23 22:39 EDT
	Nmap scan report for 192.168.1.109
	Host is up (-0.20s latency).
	All 1000 scanned ports on 192.168.1.109 are filtered
	MAC Address: 00:0C:29:E4:5D:C2 (VMware)

	Nmap done: 1 IP address (1 host up) scanned in 34.33 seconds
	[root@srv-haproxy ~]#

	
	
Link utilizados:

https://docs.netgate.com/pfsense/en/latest/packages/snort/setup.html

https://www.youtube.com/watch?v=TvQfD5oUN5o


Configurar un Outbound
https://www.youtube.com/watch?v=7MtdwPYcK24