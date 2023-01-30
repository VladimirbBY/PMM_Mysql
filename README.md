### Install Docker/Docker Compose

sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
systemctl start docker
sudo usermod -aG docker $USER

sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose

### Install PMM server (https://docs.percona.com/percona-monitoring-and-management/setting-up/server/index.html)
Run docker-compose up -d

### Set up PMM Client (https://docs.percona.com/percona-monitoring-and-management/setting-up/client/index.html)

Download the PMM Client package:

wget https://downloads.percona.com/downloads/pmm2/2.16.0/binary/tarball/pmm2-client-2.16.0.tar.gz

Unpack the package and move into the directory.

tar xfz pmm2-client-2.16.0.tar.gz && cd pmm2-client-2.16.0

export PMM_DIR=/usr/local/percona/pmm2

Run the installer.

./install_tarball

Change the path.

PATH=$PATH:$PMM_DIR/bin

Set up the agent 
pmm-agent setup --config-file=/usr/local/percona/pmm2/config/pmm-agent.yaml --server-address=192.168.1.123 --server-insecure-tls --server-username=admin --server-password=admin   

Run the agent.

pmm-agent --config-file=${PMM_DIR}/config/pmm-agent.yaml

Open a new terminal and check.

pmm-admin status

### Register your client node with PMM Server.

pmm-admin config --server-insecure-tls --server-url=https://admin:admin@X.X.X.X:443


### Setup MySQl client (https://docs.percona.com/percona-monitoring-and-management/setting-up/client/mysql.html)

On MySQL 5.7

CREATE USER 'pmm'@'*' IDENTIFIED BY 'pass' WITH MAX_USER_CONNECTIONS 10;
GRANT SELECT, PROCESS, REPLICATION CLIENT, RELOAD ON *.* TO 'pmm'@'localhost';

Add service   

With the user interface   
Select  Configuration →  PMM Inventory →  Add Instance.   

Select MySQL -- Add a remote instance.   

Enter or select values for the fields.   

Click Add service   