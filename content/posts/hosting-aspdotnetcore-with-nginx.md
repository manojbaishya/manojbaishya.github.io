+++
title = 'Hosting ASP.NET Core With Nginx on Fedora Server 40'
date = 2024-05-08T14:56:37+05:30
draft = false
+++

## Introduction

Few weeks ago, I took out my ten-year-old laptop and had it repaired and upgraded from a nearby laptop store. I had been experimenting and developing a website for my friend for over two years now (!). After many trials and experiments, I settled on a .NET Core coupled to a Vue stack. I already had a domain, but the question of hosting remained unanswered. Promptly I downloaded Fedora Server 40 and set up my old laptop with it and had it connected to the internet when installing the OS. After installing the OS, I booted it up and connected to it using PuTTy via SSH. Then I installed nginx and dotnet, published my app from Visual Studio with node and dotnet, transferred the binaries to the server via WinSCP and set up the nginx and systemd configurations.

Now the website is being served from a kestrel server with static web assets pre-built using vite. The kestrel server is linked to the nginx instance via a reverse proxy configuration. The site is only accessible on the local network so far.

## Process

Setting up Nginx with ASP.NET Core in a reverse proxy configuration is a great way to enhance the performance, scalability, and security of your web application. In this blog post, we'll walk through how to set up Nginx as a reverse proxy for an ASP.NET Core application, including the key benefits, prerequisites, and a step-by-step guide for configuration.

Nginx as a reverse proxy offers a variety of benefits for ASP.NET Core applications. These include load balancing, enhanced security, compression, and caching. By offloading static file serving and SSL termination to Nginx, your application can focus on processing dynamic requests efficiently.

To set up Nginx as a reverse proxy, first, open the Nginx configuration file, typically found at `/etc/nginx/nginx.conf` or `/etc/nginx/sites-available/default`. Add a server block for your application.

## Conclusion

Setting up Nginx as a reverse proxy for an ASP.NET Core application can significantly improve your app's performance and scalability. By following this guide, you can configure Nginx to offload static content, handle SSL termination, and serve as a robust reverse proxy. Experiment with the settings and explore additional features like caching and load balancing to further optimize your application's performance.
