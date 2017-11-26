# Install Hugo
* https://gohugo.io/getting-started/installing/
```
sudo -s
cd /opt/
wget https://github.com/gohugoio/hugo/releases/download/v0.31/hugo_0.31_Linux-64bit.tar.gz
mkdir hugo_0.31
cd hugo_0.31
tar -xvzf ../hugo_0.31_Linux-64bit.tar.gz
```

# Generate Site Skeleton
* http://gohugo.io/getting-started/quick-start/
```
cd ~/git_projects
git clone https://gitlab.com/leithsnp/leith-branch-website.git
cd leith-branch-website/
git submodule update --init --recursive
/opt/hugo_0.31/hugo new site leith-branch-website --force
# add theme
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke;echo 'theme = "ananke"' >> config.toml
```

* ** May need to compile and install updated version of Git to overcome issues with Gitlab 401 errors
* https://stackoverflow.com/questions/21820715/how-to-install-latest-version-of-git-on-centos-6-x-7-x

```
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
yum install  gcc perl-ExtUtils-MakeMaker
yum remove git
cd /usr/src
wget https://www.kernel.org/pub/software/scm/git/git-2.0.4.tar.gz
tar xzf git-2.0.4.tar.gz
cd git-2.0.4
make prefix=/usr/local/git all
make prefix=/usr/local/git install
echo 'export PATH=$PATH:/usr/local/git/bin' > /etc/profile.d/git.sh
source /etc/bashrc
git --version

/usr/local/git/bin/git config --global user.name "Johnny Hay"
/usr/local/git/bin/git config --global user.email "hayjohnny2000@yahoo.com"
/usr/local/git/bin/git clone https://gitlab.com/leithsnp/leith-branch-website.git

```

# Install Nginx and SSL Certs
* https://shawnliu.me/post/using-lets-encrypt-for-nginx-on-centos-7/
* https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-6-with-yum
* https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04
* https://www.nginx.com/blog/free-certificates-lets-encrypt-and-nginx/

```
sudo yum install -y epel-release
sudo yum install -y nginx certbot
sudo /etc/init.d/nginx start
```

# Configure Nginx for Hugo
* https://www.digitalocean.com/community/tutorials/how-to-deploy-a-hugo-site-to-production-with-git-hooks-on-ubuntu-14-04
* https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-on-centos-7
```
sudo -s
mkdir /etc/nginx/sites-available
mkdir /etc/nginx/sites-enabled
vim /etc/nginx/nginx.conf
```

```
    # Load sites-enabled
    include /etc/nginx/sites-enabled/*.conf;
    # Increase memory for parsing domain names - digitalocean - https://goo.gl/SwCiyy
    server_names_hash_bucket_size 64;
```

```
cp /etc/nginx/conf.d/default.conf /etc/nginx/sites-available/leithsnp.scot.conf
mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.bkup
vim /etc/nginx/sites-available/leithsnp.scot.conf
```

```
#
# The default server
#

server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    #server_name  _;
    #root         /usr/share/nginx/html;
    server_name  leithsnp.scot www.leithsnp.scot;
    root         /var/www/html/leithsnp;

    # Load configuration files for the default server block.
    #include /etc/nginx/default.d/*.conf;

    location / {
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }

}
```

```
sudo ln -s /etc/nginx/sites-available/leithsnp.scot.conf /etc/nginx/sites-enabled/leithsnp.scot.conf
nginx -t
sudo /etc/init.d/nginx restart
```

# Create New Draft Page
* http://gohugo.io/getting-started/quick-start/

```
cd ~/git_projects/leith-branch-website
/opt/hugo_0.31/hugo new posts/my-first-post.md
```

# Build and Deploy the Hugo Site
* https://gohugo.io/getting-started/usage/

```
cd ~/git_projects/leith-branch-website
/opt/hugo_0.31/hugo --buildDrafts
sudo cp public/* /var/www/html/leithsnp
```

# Add Netlify CMS
* https://github.com/netlify/netlify-cms/blob/master/docs/quick-start.md
* https://gohugo.io/templates/homepage/
