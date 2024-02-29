# Install Loki on Ubuntu 22.04 LTS

## Install Loki

Get the latest version tag of Loki from GitHub.

```
LOKI_VERSION=$(curl -s "https://api.github.com/repos/grafana/loki/releases/latest" | grep -Po '"tag_name": "v\K[0-9.]+')
```

Create a new directory to store Loki binary and configuration file.
```
sudo mkdir /opt/loki
```

Download archive from releases page of the Loki repository.
```
sudo wget -qO /opt/loki/loki.gz "https://github.com/grafana/loki/releases/download/v${LOKI_VERSION}/loki-linux-amd64.zip"
```

Extract binary file from archive:
```
sudo gunzip /opt/loki/loki.gz
```

Set execute permission for file:
```
sudo chmod a+x /opt/loki/loki
```

In `/usr/local/bin` directory we can create a symbolic link to the loki command:
```
sudo ln -s /opt/loki/loki /usr/local/bin/loki
```

Now loki command is available for all users as a system-wide command.\
Download configuration file for Loki:
```
sudo wget -qO /opt/loki/loki-local-config.yaml "https://raw.githubusercontent.com/grafana/loki/v${LOKI_VERSION}/cmd/loki/loki-local-config.yaml"
```

To verify installation, we can check Loki version:
```
loki -version
```

## Run Loki as a service

We can configure systemd for running Loki as a service. Create a systemd unit file:
```
sudo nano /etc/systemd/system/loki.service
```

Add the following content to the file:
```
[Unit]
Description=Loki log aggregation system
After=network.target

[Service]
ExecStart=/opt/loki/loki -config.file=/opt/loki/loki-local-config.yaml
Restart=always

[Install]
WantedBy=multi-user.target
```

Start Loki service:
```
sudo service loki start
```

To make sure that Loki service is running, you can use the following command:
```
sudo service loki status
```

Also you can stop or restart the service:
```
sudo service loki stop
sudo service loki restart
```

To enable Loki to start on boot, run the following command:
```
sudo systemctl enable loki
```

## Uninstall Loki
To completely remove the Loki, stop the service and remove a systemd unit file.
```
sudo service loki stop
sudo systemctl disable loki
sudo rm -rf /etc/systemd/system/loki.service
sudo systemctl daemon-reload
sudo systemctl reset-failed
```

Delete the installation directory:
```
sudo rm -rf /opt/loki
```

Remove symbolic link:
```
sudo rm -rf /usr/local/bin/loki
```


