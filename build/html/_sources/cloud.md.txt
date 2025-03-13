# Cloud Server

## Run the Script Locally
You don't need a cloud server if you just want to run the script multiple times automatically with a specific time interval. 

For Mac and Linux, the easiest way can be using `cron`.

Open Terminal and run:
`crontab -e`

Add the following line at the end of the file:
```
0 8 * * * [python path] [script path]`
```

The five digits describes minute, hour, day, month, and day of the week, in order. `0 8 * * *` means every day at 8 AM. 

For Windows, you can use Windows Task Scheduler.

## Using Cloud Server (DigitalOcean)
If the dataset is too big to save in your own computer, a cloud server can be unavoidable. It's just like using your computer to controll another computer remotely.

DigitalOcean seems very popular among data journalists. But they all work the same.

### Remote SSH
By creating a new "droplet" on DigitalOcean, you get an IP address and set up a password for it.

To get access, in command line:
```
ssh root@[IP address]
```
OR `ssh user@[IP address]`

The default system is Ubuntu, by far the most popular Linux distribution for running web servers. It is primarily designed to run without a graphical interface, making it lightweight and optimized for server use, however not so intuitive to control if not familar with command line.

You can also use code editor to directly write code on the cloud server. Take VS Code for example: Download Remote-SSH extension; in search bar, type in `>`, and select `Remote-SSH: Connect to host...`; type in IP address and the password. The window of code editor is now connected to the host, and you can start to create files.

### Sizing
DigitalOcean starts with $4/month, providing a basic plan with 512MB RAM, 10GB SSD and 0.5TB/month data transfer. 

To re-install scraping automatic tools like Playwright and Drissionpage, this plan is not enough. However, upsizing is very convenient: turn off the droplet, upsize, and turn on again.

When memory(RAM) is exhausted, the remote connection will be forced to break. It's always good to set up SWAP (Swap Space) as an overflow area for RAM, allowing the system to keep running when memory is overused. 1-2GB SWAP is recommended for a <2GB RAM. 

Create 1G SWAP:
```
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Make it permanent when restarting the server:
```
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Verify if it's active:
```
free -m
```