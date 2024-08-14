
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

--------------------------------------------------------------------

DELIMITER //

CREATE PROCEDURE qa_tomcatadd_log(IN p_ir_number VARCHAR(50), IN p_user_name VARCHAR(50))
BEGIN
  INSERT INTO sp_logs (ir_number, user_name) VALUES (p_ir_number, p_user_name);
END //

DELIMITER ;

DELIMITER //

CREATE PROCEDURE qa_tomcatadd(IN p_ir_number VARCHAR(50), IN p_user_name VARCHAR(50))
BEGIN
  -- Call the logging procedure
  CALL qa_tomcatadd_log(p_ir_number, p_user_name);

  DECLARE tomcat_exists INT;
  DECLARE rename_exists INT;
  DECLARE app_id INT;
  DECLARE pod_id INT;
  DECLARE tomcat_id INT;

  -- Check if Tomcat exists in tomcats table
  SELECT COUNT(*) INTO tomcat_exists FROM tomcats WHERE newname = 'p_newname';

  IF tomcat_exists = 0 THEN
    -- Tomcat doesn't exist, insert into tomcats table
    SELECT appid, podid INTO app_id, pod_id FROM appservers WHERE IP = 'p_server_ip'; -- Replace 'p_server_ip' with actual IP
    INSERT INTO tomcats (newname, tomcatlocation, portno, appid, used, emversionid, podid, active, updatedby)
    VALUES ('p_new_tomcat_name', '/eClinicalWorks/prodapps/', 'p_tomcat_port', app_id, 1, 10, pod_id, 1, 'p_updated_by'); -- Replace placeholders with actual values

    -- Check if Tomcat exists in renameaction table
    SELECT COUNT(*) INTO rename_exists FROM renameaction WHERE newname = 'p_new_tomcat_name';

    IF rename_exists = 0 THEN
      -- Tomcat doesn't exist, insert into renameaction table
      SELECT tid INTO tomcat_id FROM tomcats WHERE newname = 'p_new_tomcat_name';
      INSERT INTO renameaction (IP, tid, appid, vid, newname, complete, checked, instname, instemail, dbname, dbuname, dbpass)
      VALUES ('p_server_ip', tomcat_id, app_id, 10, 'p_new_tomcat_name', 1, 1, 'p_instance_name', 'p_instance_email', 'p_db_name', 'p_db_user', 'p_db_password'); -- Replace placeholders with actual values
    ELSE
      SET message = 'Tomcat is already present in renameaction TABLE';
    END IF;
  ELSE
    SET message = 'Tomcat is already present in tomcat table';
  END IF;
END //

DELIMITER ;

	
	
	   

--->
