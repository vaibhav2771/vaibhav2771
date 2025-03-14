
Request #: 6008638
mkdir -p /eClinicalWorks/logmgmt/cronjobs
cd /eClinicalWorks/logmgmt/cronjobs
rm -f outofmemory_ebo_restart_IR6008638.sh
rm -f outofmemory_ebo_restart_IR6008638.txt

wget --no-check-certificate "https://cloudmgr.ecwcloud.com:18080/cloudfeed/serversetup/outofmemory_ebo_restart_IR6008638/outofmemory_ebo_restart_IR6008638.txt"

mv outofmemory_ebo_restart_IR6008638.txt outofmemory_ebo_restart_IR6008638.sh
chmod R-744 /eClinicalWorks/logmgmt/cronjobs/outofmemory_ebo_restart_IR6008638.sh
dos2unix outofmemory_ebo_restart_IR6008638.sh

# Check if the cron job already exists before adding
if ! crontab -l | grep -q "outofmemory_ebo_restart_IR6008638.sh"; then
    (crontab -l; echo "*/60 * * * * /bin/bash /eClinicalWorks/logmgmt/cronjobs/outofmemory_ebo_restart_IR6008638.sh") | crontab -
fi
