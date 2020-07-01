Hub20
=========

This role provides everything necessary for setting up an instance of
[Hub20](https://github.com/mushroomlabs/hub20) with all of its
background jobs.

Requirements
------------

This role is only responsible for the installation and setup of the following hub20 services:

 - API: the REST API (a django-application, served by uvicorn)
 - web3 listen events: a background job that will listen to relevant web3 events (i.e, ETH/ERC20 transfers to accounts hold by our services, raiden channels being opened/closed, whatever else the user defines)
 - raiden sync: a background job to collect and process all events from your Raiden instance
 - track pending payments: a background job that checks pending
   transactions targeting any of the accounts of the Hub20 internal
   wallets
 
The role *DOES NOT* provide the setup of the services that hub20 depends on, namely PostgreSQL and Redis. It also does not provide any web server that should be in front of o uvicorn to serve, e.g, static media. This service is meant to be running behind some web server or load balancing system (take a look at [Uvicorn's deployment tips](https://www.uvicorn.org/deployment/) to understand your best options.
 
 This role also assumes that you are targeting a Debian-based linux distribution that uses systemd. Also, due to hub20 dependency on python >= 3.7, a more recent distro needs to be used, or you may have to add some tasks to your playbook to install a recent python on the target platform

Role Variables
--------------

 - `hub20_application_host`: (default _127.0.0.1_)
 - `hub20_application_port`: (default _5000_)
 - `hub20_blockchain_network_id`: (default _1_)
 - `hub20_blockchain_starting_block`: (default _9500000_)
 - `hub20_database_host`: (default _localhost_)
 - `hub20_database_name`: (default _hub20_)
 - `hub20_database_user`: (default _hub20_)
 - `hub20_database_port`: (default _5432_)
 - `hub20_environment_file_path`: (default _/etc/hub20_)
 - `hub20_email_backend`: (default _django.core.mail.backends.console.EmailBackend_)
 - `hub20_email_sender_address`: (default _noreply@hub20.example.com_)
 - `hub20_pip_package_name`: (default _hub20_)
 - `hub20_pip_package_state`: (default _latest_)
 - `hub20_redis_host`: (default _localhost_)
 - `hub20_redis_port`: (default _6379_)
 - `hub20_redis_celery_database`: (default _0_)
 - `hub20_redis_cache_database`: (default _1_)
 - `hub20_run_debug_mode`: (default _false_)
 - `hub20_run_python_version`: (default _python3.7_)
 - `hub20_service_web3_event_listener_enabled`: (default _true_)
 - `hub20_service_raiden_sync_enabled`: (default _true_)
 - `hub20_service_web_enabled`: (default _true_)
 - `hub20_user_name`: (default _hub20_)
 - `hub20_user_home_path`: (default _/srv/hub20_)
 - `hub20_web3_provider_uri`: (default _http://localhost:8545_)
 - `hub20_tracked_tokens`: list of token addresses (as hex strings) that will be used/accepted as currency by the stores. (default: addresses for ETH, WETH, DAI and RDN)
 - `hub20_application_static_folder_path`: (default _/srv/hub20/static__)
 - `hub20_broker_url`: (default _redis://localhost:6379/0_)
 - `hub20_cache_url`:  (default _redis://localhost:6379/1_)
 - `hub20_run_virtualenv_path`: (default _/srv/hub20/.venv"_)
 
The following variables need to be defined and should be treated as sensitive values:

 - `hub20_database_password`: the password to connect to the database.
 - `hub20_service_secret_key`: the hub20 web applications seret key is used for session managements, CSRF token generation, etc.



Example Playbook
----------------

    - hosts: hub20_servers
      roles:
         - role: geerlingguy.nginx   # Using nginx as your front proxy
         - role: lullis.certbot-nginx  # To get SSL certificates from Let's encrypt and have them served by nginx
         - role: mushroomlabs.hub20
         

License
-------

MIT 

Author Information
------------------

Raphael Lullis (raphael@mushroomlabs.com)
