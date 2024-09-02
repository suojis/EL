# Project 3: Setup Load Balancing for Static Website Using Nignx

The project involves setting up a Layer 7 load balancer using Nginx to distribute traffic between two static websites.

**Key Tasks:** Deploy three servers, set up static websites on two servers, configure Nginx on the third server as a load balancer, and update DNS records.

## Checklist

- [x] Task 1: Deploy three servers.
- [x] Task 2: Set up static websites on two servers using Nginx.
- [x] Task 3: Make a small change in the index.html file of one of the websites to differentiate between two servers OR For a clearer distinction, use two separate HTML files with distinct content.
- [x] Task 4: Set up Nginx on the third server. It will act as a load balancer.
- [x] Task 5: Configure Nginx to load and balance traffic between two static websites.
- [x] Task 6: Add the Nginx Load balancer IP to the DNS A record.
- [x] Task 7: Try accessing the website. Every time you reload the website you should see a different index.html.

## Documentation
- Spin up your 3 ubuntu servers.

![1](img/p1.PNG)

### Install Nginx and Setup Your Website

- Download your website template 

- To install Nginx, execute the following commands on your terminal.

`sudo apt update`

`sudo apt upgrade`

`sudo apt install nginx`

- Start your Nginx server by running the `sudo systemctl start nginx` command, enable it to start on boot by executing `sudo systemctl enable nginx`, and then confirm if it's running with the `sudo systemctl status nginx` command.

![2](img/p2.PNG)


> [!NOTE]
Install Nginx on both web server terminals. These are the terminals you're using to manage the servers hosting the two distinct website contents that the load balancer will distribute traffic to.

- Visit your instances IP address in a web browser to view the default Nginx startup page.

![3](img/p3.PNG)

- Execute `sudo apt install unzip` to install the unzip tool and run the following command to download and unzip your website files `sudo curl -o /var/www/html/2135_mini_finance.zip https://www.tooplate.com/zip-templates/2135_mini_finance.zip && sudo unzip -d /var/www/html/ /var/www/html/2135_mini_finance.zip && sudo rm -f /var/www/html/2135_mini_finance.zip`.

![4](img/p4.PNG)

- Set up  website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor: `sudo nano /etc/nginx/sites-available/finance`.

- Copy and paste the following code into the open text editor.

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- Edit the `root` directive within your server block to point to the directory where the downloaded website content is stored.

![5](img/p5.PNG)

- Create a symbolic link for both websites by running the following command.
`sudo ln -s /etc/nginx/sites-available/finance /etc/nginx/sites-enabled/`

![6](img/p6.PNG)

- Run the `sudo nginx -t` command to check the syntax of the Nginx configuration file, and when successful run the `sudo systemctl restart nginx` command.

- Repeat the process for the second website.

- Execute `sudo apt install unzip` to install the unzip tool and run the following command to download and unzip your website files `sudo curl -o /var/www/html/2134_gotto_job.zip https://www.tooplate.com/zip-templates/2134_gotto_job.zip && sudo unzip -d /var/www/html/ /var/www/html/2134_gotto_job.zip && sudo rm -f /var/www/html/2134_gotto_job.zip.zip`.

![7](img/p8.PNG)

- To set up  website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor: `sudo nano /etc/nginx/sites-available/Gotto`.

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- Edit the `root` directive within your server block to point to the directory where your downloaded website content is stored.

![7.1](img/p7.1.PNG)

- Create a symbolic link for both websites by running the following command.
`sudo ln -s /etc/nginx/sites-available/Gotto /etc/nginx/sites-enabled/`

- Run the `sudo nginx -t` command to check the syntax of the Nginx configuration file, and when successful run the `sudo systemctl restart nginx` command.


> [!NOTE]
On your first server, run `sudo rm /etc/nginx/sites-enabled/default`, and on your second server, run `sudo rm /etc/nginx/sites-enabled/default`. This will delete the default site-enabled folders and enable Nginx to serve content from your specified website directories. If you don't delete these default folders, you'll continue to see the default Nginx page.

- Run the `sudo systemctl restart nginx` command to restart your server.

- Check both IP addresses to confirm your website is up and running.

![9](img/p9.PNG)

![10](img/10.PNG)


### Configure your Load balancer

- Install Nginx on the server you want to use as a load balancer, and execute `sudo systemctl status nginx` to ensure it's running.

![11](img/p11.PNG)

- Execute `sudo nano /etc/nginx/nginx.conf` to edit your Nginx configuration file.


```

    upstream cloudghoul {
    server 1;
    server 2;
    # Add more servers as needed
}

server {
    listen 80;
    server_name <your domain> www.<your domain>;

    location / {
        proxy_pass http://cloudghoul;
    }
}

```

![c2](img/c2.PNG)

> [!NOTE]
 Substitute `<server 1>` and `<server 2>` with the actual private IP addresses of your servers. Also, replace `<your domain> www.<your domain>` with your root domain and subdomain name, and update proxy_pass and the other relevant fields accordingly.



- Run `sudo nginx -t` to check for syntax error.

- Apply the changes by restarting Nginx:
`sudo systemctl restart nginx`

### Create An A Record

To make your website accessible via your domain name rather than the IP address, you'll need to set up a DNS record. I did this by buying my domain from Namecheap and then moving hosting to AWS Route 53, where I set up an A record.

- Point your domain's A records to the IP address of your Nginx load balancer server.

- In route 53, select the domain name and click on **Create record**.

- Paste your **IP address➀** and then click on **Create records➁** to create the root domain.

![14](img/p14%20f.PNG)

- Click on **create record** again, to create the record for your sub domain.

- Paste your IP address➀, input the Record name(**www➁**) and then click on **Create records**➂.

- Go to the terminal you used in setting your first website and run `sudo nano /etc/nginx/sites-available/finance` to edit your settings. Enter the name of your domain and then save your settings.

![fin](img/finace.PNG)

- Restart your nginx server by running the `sudo systemctl restart nginx` command.

- Go to the terminal you used in setting the second website and run `sudo nano /etc/nginx/sites-available/Gotto` to edit your settings. Enter the name of your domain and then save your settings.

![gotto](img/Gotto.PNG)

- Restart your nginx server by running the `sudo systemctl restart nginx` command

- Go to your domain name in a web browser to verify that your website is accessible.

![p17](img/p17.PNG)


### Install certbot and Request For an SSL/TLS Certificate

- Install certbot by executing the following commands:
`sudo apt update`
`sudo apt install python3-certbot-nginx`

- Execute the `sudo certbot --nginx` command to request your certificate. Follow the instructions provided by certbot and select the domain name for which you would like to activate HTTPS.

![16](img/p16.PNG)

- Access your website to verify that Certbot has successfully enabled HTTPS.

![f](img/fffff.PNG)

![g](img/gggg.PNG)



- It is recommended to renew your LetsEncrypt certificate at least every 60 days or more frequently. You can test renewal command in dry-run mode:
`sudo certbot renew --dry-run`


