# Next.JSのAWS EC2への配備

## EC2インスタンスの作成

AMIとしてAmazon Linux 2023を選択して、インスタンスを作成する。
HTTPポートが通過できるように設定する。SSHでログインして、パッケージを更新する。

``` bash
sudo yum update -y
sudo yum upgrade -y
```

## Node.jsのインストール

Node.jsの20.xをインストールする。

``` bash
curl -sL https://rpm.nodesource.com/setup_20.x | sudo -E bash -
sudo yum install -y nodejs
node —version
npm —version
```

## gitのインストール 

``` bash
sudo yum -y install git
```

## pm2のインストール

``` bash
sudo npm install pm2 -g
```

ブート時にpm2が起動するようにsystemdに登録する。

``` bash
cd nextjs_awsdeploy
pm2 start npm --name nextjs-apps -- run start -- -p 3000
pm2 list nextjs-apps
pm2 save
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ec2-user --hp /home/ec2-user
```

## Nginxのインストール

``` bash
sudo yum install -y nginx
```

リバースプロキシを設定する。

``` bash
sudo vim /etc/nginx/conf.d/nextjs-app.conf
```

```
server {
   listen 80;
   server_name your-domain.com;
   location / {
     proxy_pass http://localhost:3000;
   }
}
```
