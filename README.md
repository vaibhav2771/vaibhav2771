INSERT INTO adhocpatches (booktype,dispname,usrgroup,delflag,uploadtime,enabledmodule,tomcat_jdk,starttime,endtime,DESCRIPTION,message,restart_tomcat) values ('adhocserverIR5586541','eRX Search Cloud IR 5586541','admin,misc','0',NOW(),'misc','0','19:00:00','07:00:00','IR-5586541 update searchcloud.scheduling.enabled=true in application.properties.','Action Performed on One Server.','yes');
 
 
insert into cookbookdetails (templatename,destfolder,podid,booktype,description,active) values ('adhocserverIR5586541','tomcatupgradecookbooks','0','adhocserverIR5586541','eRX Search Cloud IR 5586541','1');
