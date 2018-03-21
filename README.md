This is a fix for yiimp to work with the cryptocurrency "Reference line coin" / RECO.
www.referencecoin.co

Important:
In the coin configuration for RECO in the tabsheer Settings you need to deactivate
the "Txmessage" checkbox. In the tabsheet Daemon you enter "POW" for the RPC Type.


You need to 

#yiimp - yaamp fork

Required:

	linux, mysql, php, memcached


Config for nginx:

	location / {
		try_files $uri @rewrite;
	}

	location @rewrite {
		rewrite ^/(.*)$ /index.php?r=$1;
	}

	location ~ \.php$ {
		fastcgi_pass unix:/var/run/php5-fpm.sock;
		fastcgi_index index.php;
		include fastcgi_params;
	}


If you use apache, it should be something like that (already set in web/.htaccess):

	RewriteEngine on

	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^(.*) index.php?r=$1 [QSA]


If you use lighttpd, use the following config:

	$HTTP["host"] =~ "yiimp.ccminer.org" {
	        server.document-root = "/var/yaamp/web"
	        url.rewrite-if-not-file = (
			"^(.*)/([0-9]+)$" => "index.php?r=$1&id=$2",
			"^(.*)\?(.*)" => "index.php?r=$1&$2",
	                "^(.*)" => "index.php?r=$1",
	                "." => "index.php"
	        )

		url.access-deny = ( "~", ".dat", ".log" )
	}


The recommended install folder for the stratum engine is /var/stratum. Copy all the .conf files, run.sh, the stratum binary and the blocknotify binary to this folder. 

Some scripts are expecting the web folder to be /var/web. 

Add your exchange API keys in:

	web/yaamp/core/exchange/*


Look at web/yaamp/core/trading/ there are a few place where there're hardcoded withdraw BTC address (cryptsy, bittrex and bleutrade).

Edit web/serverconfig.php

You need three backend shells (in screen) running these scripts:

	web/main.sh
	web/loop2.sh
	web/block.sh

Start one stratum per algo using the run.sh script with the algo as parameter. For example, for x11:

	run.sh x11

Edit each .conf file with proper values.

Look at rc.local, it starts all three backend shells and all stratum processes. Copy it to the /etc folder so that all screen shells are started at boot up.

All your coin's config files need to blocknotify their corresponding stratum using something like:

	blocknotify=/var/stratum/blocknotify yaamp.com:port coinid %s

On the website, go to http://server.com/site/admintest to login as admin. You have to change it to something different in the code (web/yaamp/modules/site/SiteController.php).

There are logs generated in the /var/stratum folder and /var/log/stratum/debug.log for the php log.

More instructions coming as needed.

There a lot of unused code in the php branch. Lot come from other projects I worked on and I've been lazy to clean it up before to integrate it to yaamp. It's mostly based on the Yii framework which implements a lightweight MVC.

	http://www.yiiframework.com/


