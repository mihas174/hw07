Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: <<-SHELL
sudo yum -y install wget \
redhat-lsb-core \
rpmdevtools \
rpm-build \
createrepo \
yum-utils \
gcc \
ca-certificates \
yum-plugin-downloadonly \
tree
SHELL

config.vm.provision "shell", inline: <<-SHELL, privileged: false
wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.20.2-1.el7.ngx.src.rpm
rpm -i nginx-1.20.2-1.el7.ngx.src.rpm
sudo yum-builddep -y rpmbuild/SPECS/nginx.spec
wget https://www.openssl.org/source/old/1.1.1/openssl-1.1.1l.tar.gz
tar -xvf openssl-1.1.1l.tar.gz
sed -i '/--with-ld-opt="%{WITH_LD_OPT}" /a '    --with-openssl=/home/vagrant/openssl-1.1.1l'' /home/vagrant/rpmbuild/SPECS/nginx.spec
sed -i "s/--with-debug.*/--with-debug/g" /home/vagrant/rpmbuild/SPECS/nginx.spec
rpmbuild -bb /home/vagrant/rpmbuild/SPECS/nginx.spec
sudo yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/nginx-1.20.2-1.el7.ngx.x86_64.rpm
sudo systemctl enable nginx
sudo systemctl start nginx
sudo mkdir /usr/share/nginx/html/repo/
sudo cp /home/vagrant/rpmbuild/RPMS/x86_64/nginx-1.20.2-1.el7.ngx.x86_64.rpm /usr/share/nginx/html/repo/
sudo createrepo /usr/share/nginx/html/repo/
sudo sed -i '/ index.html index.htm;/a  autoindex on;' /etc/nginx/conf.d/default.conf
sudo nginx -t
sudo nginx -s reload
sudo touch /etc/yum.repos.d/my_repo.repo
cat >> /etc/yum.repos.d/my_repo.repo << EOF
[my_repo]
name=my_repo
baseurl=http://localhost/repo
gpgcheck=0
enabled=1
EOF
SHELL
end

