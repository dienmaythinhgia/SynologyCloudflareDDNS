# Synology Cloudflare DDNS Script 📜

The is a script to be used to add [Cloudflare](https://www.cloudflare.com/) as a DDNS to [Synology](https://www.synology.com/) NAS. The script used an updated API, Cloudflare API v4.

## How to use

### Access Synology via SSH

1. Login to your DSM
2. Go to Control Panel > Terminal & SNMP > Enable SSH service
3. Use your client to access Synology via SSH.
4. Use your Synology admin account to connect.

### Run commands in Synology

1. Download `cloudflareddns.sh` from this repository to `/sbin/cloudflareddns.sh`

```
#sudo -i   (into root)
wget https://raw.githubusercontent.com/joshuaavalon/SynologyCloudflareDDNS/master/cloudflareddns.sh -O /sbin/cloudflareddns.sh
```

It is not a must, you can put I whatever you want. If you put the script in other name or path, make sure you use the right path.

2. Give others execute permission

```
chmod +x /sbin/cloudflareddns.sh
```

3. Add `cloudflareddns.sh` to Synology

```
cat >> /etc.defaults/ddns_provider.conf << 'EOF'
[Cloudflare]
        modulepath=/sbin/cloudflareddns.sh
        queryurl=https://www.cloudflare.com
        website=https://www.cloudflare.com

```

`queryurl` does not matter because we are going to use our script but it is needed.
A safe Python environment setup
Ths module needs some Python modules installable via pip, namely tldextract. It will help us to automatically fetch parent domain.

Synology is known for wiping custom Python module you install via pip. This is why we're better of creating a virtualenv at /usr/local or other safe location. In the furue we will convert the entire script to Python.

curl https://bootstrap.pypa.io/get-pip.py | python
pip install virtualenv
# create virtualenv twice to ensure creation of "activate" script
virtualenv /usr/local/SynologyCloudflareDDNS
virtualenv /usr/local/SynologyCloudflareDDNS
# go inside our virtualenv
. /usr/local/SynologyCloudflareDDNS/bin/activate
# install the packages we need there (cloudflare will be used in the future):
pip install tldextract cloudflare


### Get Cloudflare parameters

1. Go to your domain overview page and copy your zone ID.
2. Go to your profile > **API Tokens** > **Create Token**. It should have the permissions of `Zone > DNS > Edit`. Copy the api token.

### Setup DDNS

1. Login to your DSM
2. Go to Control Panel > External Access > DDNS > Add
3. Enter the following:
   - Service provider: `Cloudflare`
   - Hostname: `www.example.com`
   - Username/Email: `<Zone ID>`
   - Password Key: `<API Token>`
