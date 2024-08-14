
DELIMITER //

CREATE PROCEDURE qa_tomcatadd(
    IN p_newname VARCHAR(15),        
    IN p_portno INT,
	IN p_IP INT,
    IN p_instname VARCHAR(50), 
    IN p_instemail VARCHAR(100),
    IN p_dbname VARCHAR(20),
    IN p_dbuname VARCHAR(15),
    IN p_dbpass VARCHAR(50)
)
BEGIN
    
	DECLARE tomcatname VARCHAR(15)
	DECLARE tomcatrename VARCHAR (15) 
	DECLARE v_appid INT;
    DECLARE v_podid INT;
	DECLARE v_tid INT;
   	
	SET tomcatname = (SELECT * FROM tomcats WHERE newname='p_newname');
	SET tomcatrename = (SELECT * FROM renameaction WHERE newname='p_newname');
	IF tomcatname = 0 THEN 
	   
	   SET v_appid = (SELECT appid FROM appservers WHERE IP='p_IP'; 
	   SET v_podid = (SELECT podid FROM appservers WHERE IP='p_IP';
	   INSERT INTO tomcats (newname,tomcatlocation,portno,appid,used,emversionid,podid,active,updatedby) VALUES ('p_newname','/eClinicalWorks/prodapps/','p_portno','v_appid','1','10','v_podid','1','p_instname');
	IF tomcatrename = 0 THEN	
	  SET v_appid = (SELECT appid FROM appservers WHERE IP='p_IP';
	  SET v_tid = (SELECT tid from tomcats WHERE newname='p_newname';
	  INSERT INTO renameaction (IP,tid,appid,vid,newname,complete,checked,instname, instemail,dbname,dbuname,dbpass) VALUES ('p_IP','v_tid','v_appid','10','p_newname','1','1','p_instname','p_instemail','p_dbname','p_dbuname','p_dbpass');  	   
    ELSE 
       SET message = "Tomcat is already present in renameaction TABLE";
	 END IF;
	ELSE   
	   SET message = "Tomcat is already present in tomcat table";
	END IF;	

END //

DELIMITER ;
	
	
	   
