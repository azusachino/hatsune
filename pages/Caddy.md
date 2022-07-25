- #CS
- Caddy is a powerful, extensible platform to serve your sites, services, and apps, written in Go. If you're new to Caddy, the way you serve the Web is about to change.
- ## Contents
	- ### Regularly Used Commands
	  collapsed:: true
		- `caddy run`
			- start caddy server (may load the `Caddyfile` in cwd)
			- `caddy run --config /path/to/Caddyfile`
			- `caddy run --watch` watch config file changes
		- `caddy start` - start caddy server and have it run in the background
		- `caddy stop` - stop the caddy process
		- `caddy reload` - for a graceful config change
		- `caddy adapt` - convert `Caddyfile` to json structure
	- The `Caddyfile`
	  collapsed:: true
		- First site
		- Static file server
		- Templates
		- Compression
			- ```caddy
			  localhost
			  
			  encode zstd gzip
			  templates
			  file_server browse
			  ```
		- Multiple sites
			- ```caddy
			  :8080 {
			  	respond "I am 8080"
			  }
			  
			  :8081 {
			  	respond "I am 8081"
			  }
			  :8082, :8083 {
			      respond "I am 808*"
			  }
			  ```
		- Matchers
			- ```caddy
			  localhost
			  
			  file_server
			  reverse_proxy /api/* 127.0.0.1:9005
			  ```
		- Environment variables
			- `export SITE_ADDRESS=localhost:9055`
			- ```caddy
			  {$SITE_ADDRESS}
			  
			  file_server
			  ```
		- Comments
			- ```caddy
			  :2015
			  # 	hello
			  respond "Hello Caddy!"
			  ```
	- ### Static Files
		- `caddy file-server --listen :2015`
		- `caddy file-server --browse` - display a file listing (without index)
		- `caddy file-server --root ~/site` - use specific folder as site root
	- ### Reverse Proxy
		- `caddy reverse-proxy --from :2016 --to 127.0.0.1:9000`
		- ```caddy
		  localhost
		  
		  reverse_proxy 127.0.0.1:9000
		  ```
- ## References
	- [Caddy Official](https://caddyserver.com/docs/)