# Nginx Reverse Proxy for Tableau Cloud on Heroku

**Note**: This setup only works with Tableau Embedded Analytics and Connected Apps (JWT) authentication. It is not compatible with SAML, OIDC, or any other authentication methods, nor does it work for direct access to the Tableau Cloud portal interface. These limitations apply only to Tableau Cloud; Tableau Server supports all authentication methods and access types.

# Streamlining Tableau Embeddings with a Reverse Proxy

There's a good reason to host your Tableau visualizations (embedded content) on the same web address (domain) as your main website (parent portal). This ensures that cookies created by Tableau are recognized as coming from your own site (first-party cookies). This approach makes everything work smoothly across different web browsers and apps, including Apple's Safari browser and apps on iPhones and iPads.

By using a reverse proxy, you can achieve this domain alignment even if Tableau Server or Tableau Cloud resides on a separate system behind the scenes. The reverse proxy acts as an intermediary, forwarding requests from users to Tableau Server/Cloud and delivering the response back. This way, browsers see all interactions as happening on your main website's domain, eliminating concerns about third-party cookie blocking.

## Project Overview

This project sets up an Nginx reverse proxy on Heroku to handle multiple subdomains for Tableau Online servers, specifically for embedded analytics and Connected Apps authentication. The configuration proxies requests for `10ax.eskihub.com` and `dub01.eskihub.com` to their respective Tableau Online instances. Replace `eskihub.com` with your own domain.


**Example:**
- **Your Portal:** `eskihub.com`
- **Tableau Embedded Visualizations:** `analytics.eskihub.com`

Ensure your Nginx configuration includes the following line to share cookies across all subdomains:

- `proxy_cookie_domain '10ax.online.tableau.com' 'eskihub.com';`

The Tableau embedded visualization URL should follow this format:

- `https://{tableau-pod}.{your-domain}/t/{site-name}/views/{workbook-name}/{view-name}`

## Configuration

The Nginx configuration supports two subdomains (add more as needed):

- 10ax.eskihub.com -> 10ax.online.tableau.com
- dub01.eskihub.com -> dub01.online.tableau.com

## Files

- `Procfile`: Defines the command to start Nginx on Heroku.
- `config/nginx.conf.erb`: The Nginx configuration file with reverse proxy set for a few Tableau Cloud pods.

 ## Steps to Setup using the Heroku CLI

1. Clone the Repository:

```bash
git clone https://github.com/alexeski/nginx-reverseproxy-eskihub-heroku.git
cd nginx-reverseproxy-eskihub-heroku
```

2. Create a New Heroku App:

```bash
heroku create your-app-name
```

3. Add the Nginx buildpack to your Heroku app:

```bash
heroku buildpacks:add https://github.com/heroku/heroku-buildpack-nginx -a your-app-name
```

4. Add your custom domains to the Heroku app:

Example with the Heroku CLI:
```bash
heroku domains:add 10ax.{your-domain} -a your-app-name
heroku domains:add dub01.{your-domain} -a your-app-name
```
5. Set the Necessary Environment Variable:

```bash
heroku config:set NGINX_APP_ENABLE=true -a your-app-name
```

6. Deploy the App to Heroku:

```bash
git push heroku main
```

7. Update Your DNS Settings to Point Your Subdomains to the Heroku App's DNS Target. Find the DNS target using:
```bash
heroku domains -a your-app-name
```
8. Enable SSL for your custom domains:
```bash
heroku certs:auto:enable -a your-app-name
```

# Additional Information

Make sure to replace all instances of eskihub.com with your own domain. This will ensure that all configurations are correctly set up for your domain.
