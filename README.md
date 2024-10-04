
DELIMITER //

CREATE PROCEDURE insert_appserver(
    IN p_name VARCHAR(255),
    IN p_hostname VARCHAR(255),
    IN p_chef_nodename VARCHAR(255),
    IN p_ip VARCHAR(50),
    IN p_podid INT,
    IN p_apptype VARCHAR(50)
)
BEGIN
    -- Check if the entry already exists
    IF EXISTS (SELECT 1 FROM appservers 
               WHERE name = p_name 
               AND hostname = p_hostname 
               AND chef_nodename = p_chef_nodename 
               AND ip = p_ip 
               AND podid = p_podid 
               AND apptype = p_apptype) THEN
        -- Throw message if entry exists
        SELECT 'Entry already present!';
    ELSE
        -- Insert new entry if not exists
        INSERT INTO appservers (
            name, hostname, chef_nodename, ip, podid, apptype, active, liveserver, appostype
        ) VALUES (
            p_name, p_hostname, p_chef_nodename, p_ip, p_podid, p_apptype, '1', '1', 'lin'
        );
        -- Success message
        SELECT 'Entry successfully added!';
    END IF;
END //

DELIMITER ;






sed -i 's/\(<Resource name="jdbc\/voiceds1"[^>]*\)\(url="[^"]*"\)/\1$(grep -m 1 "url=" context.xml)/g; s/\1\(username="[^"]*"\)/\1$(grep -m 1 "username=" context.xml)/g; s/\1\(password="[^"]*"\)/\1$(grep -m 1 "password=" context.xml)/g' aftercontext.xml



sed -i 's/\(<Resource name="jdbc\/voiceds1"[^>]*\)\(url="[^"]*"\)/\1$(grep -m 1 "url=" context.xml)/g; s/\1\(username="[^"]*"\)/\1$(grep -m 1 "username=" context.xml)/g; s/\1\(password="[^"]*"\)/\1$(grep -m 1 "password=" context.xml)/g' aftercontext.xml



sed -i 's/\(<Resource name="jdbc\/voiceds1"[^>]*\)\(url="[^"]*"\)/\1$(grep -m 1 "url=" context.xml)/g; s/\1\(username="[^"]*"\)/\1$(grep -m 1 "username=" context.xml)/g; s/\1\(password="[^"]*"\)/\1$(grep -m 1 "password=" context.xml)/g' aftercontext.xml




sed -i 's/\(url="[^"]*"\)/$(grep -m 1 "url=" context.xml)/g; s/\(username="[^"]*"\)/$(grep -m 1 "username=" context.xml)/g; s/\(password="[^"]*"\)/$(grep -m 1 "password=" context.xml)/g' aftercontext.xml




sed -i 's/\(url="[A"]*"\)/$(grep -m 1 "url=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g; s/\(username="[^"]*"\)/$(grep -m 1 "username=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g; s/\(password="[^"]*"\)/$(grep -m 1 "password=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g' eclinicalworks/ecwinstall/detail/testing/aftercontext.xml

sed -i 's/\(url="[^"]*"\)/$(grep -m 1 "url=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g; s/\(username="[^"]*"\)/$(grep -m 1 "username=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g; s/\(password="[^"]*"\)/$(grep -m 1 "password=" eclinicalworks/ecwinstall/detail/testing/context.xml)/g' eclinicalworks/ecwinstall/detail/testing/aftercontext.xml






sed -i 's/\(url="[^"]*"\)/$(grep -m 1 "url=" /backup/abc.properties)/g; s/\(username="[^"]*"\)/$(grep -m 1 "username=" /backup/abc.properties)/g; s/\(password="[^"]*"\)/$(grep -m 1 "password=" /backup/abc.properties)/g' /ecw/abc.properties

sed -i '$ a\
enableSecDecoratorFramework=yes' /eClinicalWorks/ecwinstall/vaibhav/properties.txt



Recipe:: default
#
# Copyright 2014, YOUR_COMPANY_NAME
#
# All rights reserved - Do Not Redistribute
#

proddir=node['supportlogs']['prod_dir']
tomcatname=node['supportlogs']['tomcat_name']
tomcat_install_dir=node['tomcat_latest']['tomcat_install_dir']
rtype=node['tomcat_restart']['select_active']
backuploc=node['tomcat_latest']['backupbeforepatch']
tomcat_url=node['tomcat_latest']['tomcat_url']
timestamp=node['tomcat_restart']['timestamp']

script"Stop Sophos" do
  interpreter "bash"
  code <<-EOH

 if [ -f /etc/init.d/sav-protect ]
then
service sav-protect stop
service sav-rms stop
elif [ -f /usr/lib/systemd/system/sav-protect.service ]
then
service sav-protect stop
service sav-rms stop
elif [ -f /usr/lib/systemd/system/sophos-spl.service ]
then
systemctl stop sophos-spl
fi

  mkdir -p #{tomcat_install_dir}
  EOH
end

script "Download_ecwencr" do
  not_if {File.exists?("#{tomcat_install_dir}/ecwencrypt.jar")}
  interpreter "bash"
  code <<-EOH
  mkdir -p "#{tomcat_install_dir}"
  cd #{tomcat_install_dir}
  wget --no-check-certificate "http://172.25.7.151:8001/cloudfeed/serversetup/ecwencrypt.jar.txt"
  mv ecwencrypt.jar.txt ecwencrypt.jar
  chown -R root:root #{tomcat_install_dir}/ecwencrypt.jar
  chmod 755 #{tomcat_install_dir}/ecwencrypt.jar
  EOH
end

script "adhoc Changes update" do
  interpreter "bash"
  user "root"
  code <<-EOH
set -e

    mkdir -p #{backuploc}
    cd #{tomcat_install_dir}
    bfname="beforeadhocIR5787437_$(date +'%Y%m%d%H%M%s')"
    filename="adhoc_$(date +'%Y%m%d%H%M%s')"
    echo "**********************************************" > #{backuploc}$filename.txt
    echo "$(date +'%b %d %k:%M:%S') Chef Result for $(hostname)" >> #{backuploc}$filename.txt 2>&1
    echo "**********************************************" >> #{backuploc}$filename.txt 2>&1
	
if [ -f #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties ]
then
service #{tomcatname} stop
mkdir -p #{backuploc}$bfname/#{tomcatname}/webapps/ecwWebFax/WEB-INF
yes | cp #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties #{backuploc}$bfname/#{tomcatname}/webapps/ecwWebFax/WEB-INF/.

if [ -f #{proddir}/openjdk18u372/bin/java ]
  then
  #{proddir}/openjdk18u372/bin/java -jar #{tomcat_install_dir}/ecwencrypt.jar #{proddir}/#{tomcatname} rem-auto >>  #{backuploc}/$filename.txt


echo "************************" >>  #{backuploc}/$filename.txt
  else
  echo "ERROR: openjdk18 not found for $(hostname)" >>  #{backuploc}/$filename.txt
  fi
  else
  echo "ERROR: Project.properties not found for ${tomcatname}" >> #{backuploc}/$filename.txt
  echo "************************" >>  #{backuploc}/$filename.txt
  fi
chown tomcat:tomcat #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties
restorecon -R #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties

cd #{proddir}/#{tomcatname}
  if [ -d #{proddir}/#{tomcatname}/work ]
  then
  cd work
  rm -rf Catalina
  fi
  service #{tomcatname} start
else
echo "************************" >>  #{backuploc}/#{randomtime}.txt
echo "ERROR: TOMCAT not found #{tomcatname}" >> #{backuploc}/#{randomtime}.txt
echo "************************" >>  #{backuploc}/#{randomtime}.txt
fi


cd #{backuploc}
curl -k -F "fileToUpload=@$filename.txt"  -F "accessauth=practicelogs" http://172.25.7.151:8001/chefphp/uploadadhocfiles.php
echo "File Download Link: http://172.25.7.151:8001//cloudfeed/supportlogs/practicelogs/$filename.txt"  >  #{backuploc}/result.txt


  EOH
end

script"Start Sophos" do
  interpreter "bash"
  code <<-EOH

if [ -f /etc/init.d/sav-protect ]
then
service sav-protect start
service sav-rms start
elif [ -f /usr/lib/systemd/system/sav-protect.service ]
then
service sav-protect start
service sav-rms start
elif [ -# Cookbook Name:: adhocIR5787437
# Recipe:: default
#
# Copyright 2014, YOUR_COMPANY_NAME
#
# All rights reserved - Do Not Redistribute
#

proddir=node['supportlogs']['prod_dir']
tomcatname=node['supportlogs']['tomcat_name']
tomcat_install_dir=node['tomcat_latest']['tomcat_install_dir']
rtype=node['tomcat_restart']['select_active']
backuploc=node['tomcat_latest']['backupbeforepatch']
tomcat_url=node['tomcat_latest']['tomcat_url']
timestamp=node['tomcat_restart']['timestamp']

script"Stop Sophos" do
  interpreter "bash"
  code <<-EOH

 if [ -f /etc/init.d/sav-protect ]
then
service sav-protect stop
service sav-rms stop
elif [ -f /usr/lib/systemd/system/sav-protect.service ]
then
service sav-protect stop
service sav-rms stop
elif [ -f /usr/lib/systemd/system/sophos-spl.service ]
then
systemctl stop sophos-spl
fi

  mkdir -p #{tomcat_install_dir}
  EOH
end

script "Download_ecwencr" do
  not_if {File.exists?("#{tomcat_install_dir}/ecwencrypt.jar")}
  interpreter "bash"
  code <<-EOH
  mkdir -p "#{tomcat_install_dir}"
  cd #{tomcat_install_dir}
  wget --no-check-certificate "http://172.25.7.151:8001/cloudfeed/serversetup/ecwencrypt.jar.txt"
  mv ecwencrypt.jar.txt ecwencrypt.jar
  chown -R root:root #{tomcat_install_dir}/ecwencrypt.jar
  chmod 755 #{tomcat_install_dir}/ecwencrypt.jar
  EOH
end

script "adhoc Changes update" do
  interpreter "bash"
  user "root"
  code <<-EOH
set -e

    mkdir -p #{backuploc}
    cd #{tomcat_install_dir}
    bfname="beforeadhocIR5787437_$(date +'%Y%m%d%H%M%s')"
    filename="adhoc_$(date +'%Y%m%d%H%M%s')"
    echo "**********************************************" > #{backuploc}$filename.txt
    echo "$(date +'%b %d %k:%M:%S') Chef Result for $(hostname)" >> #{backuploc}$filename.txt 2>&1
    echo "**********************************************" >> #{backuploc}$filename.txt 2>&1
	
if [ -f #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties ]
then
service #{tomcatname} stop
mkdir -p #{backuploc}$bfname/#{tomcatname}/webapps/ecwWebFax/WEB-INF
yes | cp #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties #{backuploc}$bfname/#{tomcatname}/webapps/ecwWebFax/WEB-INF/.

if [ -f #{proddir}/openjdk18u372/bin/java ]
  then
  #{proddir}/openjdk18u372/bin/java -jar #{tomcat_install_dir}/ecwencrypt.jar #{proddir}/#{tomcatname} rem-auto >>  #{backuploc}/$filename.txt


echo "************************" >>  #{backuploc}/$filename.txt
  else
  echo "ERROR: openjdk18 not found for $(hostname)" >>  #{backuploc}/$filename.txt
  fi
  else
  echo "ERROR: Project.properties not found for ${tomcatname}" >> #{backuploc}/$filename.txt
  echo "************************" >>  #{backuploc}/$filename.txt
  fi
chown tomcat:tomcat #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties
restorecon -R #{proddir}/#{tomcatname}/webapps/ecwWebFax/WEB-INF/Project.properties

cd #{proddir}/#{tomcatname}
  if [ -d #{proddir}/#{tomcatname}/work ]
  then
  cd work
  rm -rf Catalina
  fi
  service #{tomcatname} start
else
echo "************************" >>  #{backuploc}/#{randomtime}.txt
echo "ERROR: TOMCAT not found #{tomcatname}" >> #{backuploc}/#{randomtime}.txt
echo "************************" >>  #{backuploc}/#{randomtime}.txt
fi


cd #{backuploc}
curl -k -F "fileToUpload=@$filename.txt"  -F "accessauth=practicelogs" http://172.25.7.151:8001/chefphp/uploadadhocfiles.php
echo "File Download Link: http://172.25.7.151:8001//cloudfeed/supportlogs/practicelogs/$filename.txt"  >  #{backuploc}/result.txt


  EOH
end

script"Start Sophos" do
  interpreter "bash"
  code <<-EOH

if [ -f /etc/init.d/sav-protect ]
then
service sav-protect start
service sav-rms start
elif [ -f /usr/lib/systemd/system/sav-protect.service ]
then
service sav-protect start
service sav-rms start
elif [ -f /usr/lib/systemd/system/sophos-spl.service ]
then
systemctl start sophos-spl
fi

  EOH
end

f /usr/lib/systemd/system/sophos-spl.service ]
then
systemctl start sophos-spl
fi

  EOH
end

