# Back to Basics - Episode 2

This repo has the necessary files to take you through capabilities as listed below which you can achieve with NGINX & NGINX Plus. 

1. **Metrics Dashboard** - View metrics and add/update upstream servers *(N+ Only)*
2. **Metrics** - Look at alternative options for capturing NGINX metrics, stub_status & prometheus integeration *(N+ Only)*
3. **Key-value store** - Configure IP Allow/Deny List *(N+ Only)*
4. **Key-value store** - Dynamic SSL Certificate Management *(N+ Only)*
5. **Cache Management** - Configure caching & enable headers to identify if cache was "HIT" or "MISS"
6. **JWT Validation** - Generate tokens from https://jwt.io and validate JWT and re-direct based on value from the jwt payload *(N+ Only)*


Link to YouTube Video:
* [LINK](comming soon !)

### Prerequisites

What will you require to run these configuration files as-is:

* Ubuntu 18.04 VM with sudo access
  * Ensure you have the static files (App1, App2, App3, covid-app & jwt-app) available in location *"/opt/services"*
  * You have included a hosts file entry;
  `<internal-ip>	example.com www.example.com example123.com www.example123.com`

* NGINX Plus *(R22)*


### Metrics Dashboard | ep1.dashboard.conf

* This .conf file serves 3 Apps on ports 9001, 9002 & 9003
* Load Balancing is configured on port 9000 ( LB'ing across 9001 & 9002 only )
* API Dashboard is being served on port 8080

Access the Dashboard and edit the "backend_servers"; "Add server" with address 127.0.0.1:9003, click set-state to "Up" and click Add. 
Access the localhost on port 9000 and view the App3 in the rotation when you refresh.

##### Useful Links:
* [Live Activity Monitoring with N+](https://docs.nginx.com/nginx/admin-guide/monitoring/live-activity-monitoring/)
* [Stub Status Module for NGINX](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html)
* [Prometheus Exporter](https://github.com/nginxinc/nginx-prometheus-exporter)


### Key-value Store | key.value.store.conf

#### IP Allow/Deny List
Enable a key-value zone & keyval for the allowlist_zone datastore. 
* `curl -X GET 'http://<internal-ip>:8080/api/6/http/keyvals/allowlist_zone'`
which should return an empty response `{}`
* **Note:** Because we are on making the changes on the same VM, we can perform the same task via localhost [127.0.0.1]
* `curl -X POST -d ‘{“<internal-ip>":”1"}' -s 'http://localhost:8080/api/6/http/keyvals/allowlist_zone’`
- We have now blocked access from internal IP, `1` for Deny and `0` for Allow. 
* Running GET agains the <internal-ip> should now throw a 403
* `curl -X GET 'http://<internal-ip>:8080/api/6/http/keyvals/allowlist_zone'`
If you are presented with '403 Forbidden', we have successfully enabled IP Allow/Deny list.  


#### SSL Certificate Management
This .conf takes you through the use of key-value store for SSL Management in a few steps [Please don't use this .conf file as is]
* Step 1: Serving content on PORT 8443 - non-secure
* Step 2: Serving content on PORT 8443 - secure | SSL Cert & Key are stored on disk
* Step 3: Enabled key-value store for storing key & cert files. Variable $ssl_server_name matches the request host name with the name of crt/key
  * Try accessing www.example123.com, which should throw an error. 
* Step 4: Dynamically add a crt/key for www.example123.com and revist the page again, now to be able to view the content. 

##### Useful Links:
* [SSL Termination](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)
* [Dynamic SSL Key Management: YouTube](https://www.youtube.com/watch?v=aeLE988jmlo&ab_channel=NGINX%2CInc)
* [$ssl_server_name - Variable](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#var_ssl_server_name)


### Cache Management | cache.management.conf
This .conf takes you through enabling Caching   
* With the .conf file included, you can see that we have enabled `proxy_cache_path`   
* Within the location block of your server, we have used a few directives to capture a few additional Headers and defined the `proxy_cache` and it's validity    
* **Note:** You can't cache content from within the same server block, you have to go out to another server    

##### Useful Links:
* [Content Caching](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)
* [A Detailed Guide to Caching: Blog](https://www.nginx.com/blog/nginx-caching-guide/)
* [$proxy_cache - Directive](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache)





## Troubleshooting

* Check the prmisssions on the .crt & key if you see errors in NGINX with `$ssl_server_name` variable. 
* Ensure that you have verified that the .crt/.key files are both valid. 

## Built With

* [Ubuntu](https://ubuntu.com/) - My favourite Linux OS for testing
* [NGINX Plus](https://www.nginx.com/free-trial-request/) - NGINX Plus Trial


## Author

* **Jay Desai** - *Other Repos* - [jay-nginx](https://github.com/jay-nginx)

