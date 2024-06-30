# Nginx Reverse Proxy for Tableau Cloud on Heroku
# Only works with Tableau Embedded Analytics and Connected Apps (jwt) authentication

This project sets up an Nginx reverse proxy on Heroku to handle multiple subdomains for Tableau Online servers. It's configured to proxy requests for 10ax.eskihub.com and dub01.eskihub.com to their respective Tableau Online instances.

Notice this line in the config file which ensures ookies will be shared across all subdomains of eskihub.com: 
proxy_cookie_domain '10ax.online.tableau.com' 'eskihub.com';


## Configuration

The Nginx configuration supports two subdomains:

- 10ax.eskihub.com -> 10ax.online.tableau.com
- dub01.eskihub.com -> dub01.online.tableau.com

## Files

- `Procfile`: Defines the command to start Nginx on Heroku.
- `config/nginx.conf.erb`: The Nginx configuration file with reverse proxy set for a few Tableau Cloud pods, add others as needed.

# Add the Nginx buildpack to your Heroku app:

heroku buildpacks:add https://github.com/heroku/heroku-buildpack-nginx -a your-app-name

# Add your custom domains to the Heroku app:

Example with the Heroku CLI:
heroku domains:add 10ax.eskihub.com -a your-app-name
heroku domains:add dub01.eskihub.com -a your-app-name

# Environment variables:

NGINX_APP_ENABLE=true

# Update your DNS settings to point your subdomains to the Heroku app's DNS target. You can find the DNS target with (CLI):
heroku domains

# Enable SSL for your custom domains:
heroku certs:auto:enable
