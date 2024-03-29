# FYP4001

##For GIT installation of both new and old menu, follow the guide below. 

Install git:


sudo apt install -y git
Clone IOTstack:

If you want "new menu":


git clone https://github.com/SensorsIot/IOTstack.git ~/IOTstack
If you prefer "old menu":


git clone -b old-menu https://github.com/SensorsIot/IOTstack.git ~/IOTstack
Run the menu and choose your containers:


cd ~/IOTstack
./menu.sh
Note:

If you are running "old menu" for the first time, you will be guided to "Install Docker". That will end in a reboot, after which you should re-enter the menu and choose your containers.
Bring up your stack:


cd ~/IOTstack
docker-compose up -d


These changes mean that networking is **identical** under both *old* and *new* menus. To summarise the changes:

1. Only two internal networks are defined – as follows:

	* "default" which adopts the name `iotstack_default` at runtime.
	* "nextcloud" which adopts the name `iotstack_nextcloud` at runtime.
	
	If you are using docker-compose v2.0.0 or later then the `iotstack_nextcloud` network will only be instantiated if you select NextCloud as one of your services. Earlier versions of docker-compose instantiate all networks even if no service uses them (which is why you get those warnings at "up" time).

2. The only service definitions which now have `networks:` directives are:

	* NextCloud: joins the "default" and "nextcloud" networks; and
	* NextCloud_DB: joins the "nextcloud" network.
	
	All other containers will join the "default" network, automatically, without needing any `networks:` directives.

#### <a name="networkHandEdit"> adopting networking changes by hand </a>

If you maintain your `docker-compose.yml` by hand, you can adopt the networking changes by doing the following:

1. Take your stack down. This causes Docker to remove any existing networks. 
2. Remove **all** `networks:` directives wherever they appear in your `docker-compose.yml`. That includes: 

	* the `networks:` directives in all service definitions; and
	* the `networks:` specifications at the end of the file.

3. Append the contents of the following file to your `docker-compose.yml`:

	```
	~/IOTstack/.templates/env.yml
	```

	For example:
	
	```
	$ cat ~/IOTstack/.templates/env.yml >>~/IOTstack/docker-compose.yml
	```
	
	The `env.yml` file is the same for both *old-menu* and *master* branches.

4. If you run the NextCloud service then:

	* Add these lines to the NextCloud service definition:

		```
		networks:
		  - default
		  - nextcloud
		```

	* Add these lines to the NextCloud_DB service definition:

		```
		networks:
		  - nextcloud
		```

5. Bring up your stack.	

### contributions
