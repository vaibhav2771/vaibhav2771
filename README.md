
-- Start of the SQL Script

-- 1. Create the sp_logs Table
DELIMITER //

CREATE TABLE sp_logs (
    request_number VARCHAR(50),
    execution_date DATETIME,
    executed_by VARCHAR(50),
    procedure_name VARCHAR(100)
);

DELIMITER ;

-- 2. Create the Helper Stored Procedure
DELIMITER //

CREATE PROCEDURE log_and_run_qa_tomcatadd(
    IN p_newname VARCHAR(15),
    IN p_portno INT, 
    IN p_IP INT, 
    IN p_instname VARCHAR(50), 
    IN p_instemail VARCHAR(100), 
    IN p_dbname VARCHAR(20), 
    IN p_dbuname VARCHAR(15), 
    IN p_dbpass VARCHAR(50),
    IN p_request_number VARCHAR(50),
    IN p_execution_date DATETIME,
    IN p_executed_by VARCHAR(50)
)
BEGIN
    -- Log the procedure execution
    INSERT INTO sp_logs (request_number, execution_date, executed_by, procedure_name)
    VALUES (p_request_number, p_execution_date, p_executed_by, 'qa_tomcatadd');

    -- Call the actual procedure
    CALL qa_tomcatadd(
        p_newname, p_portno, p_IP, p_instname, 
        p_instemail, p_dbname, p_dbuname, p_dbpass
    );
END //

DELIMITER ;

-- 3. Create or Replace the qa_tomcatadd Stored Procedure
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

    DECLARE tomcatname VARCHAR(15);
    DECLARE tomcatrename VARCHAR (15); 
    DECLARE v_appid INT;
    DECLARE v_podid INT;
    DECLARE v_tid INT;
    
    -- Check if the tomcatname already exists
    SET tomcatname = (SELECT newname FROM tomcats WHERE newname = p_newname);
    SET tomcatrename = (SELECT newname FROM renameaction WHERE newname = p_newname);

    IF tomcatname IS NULL THEN
        -- Fetch application ID and pod ID
        SET v_appid = (SELECT appid FROM appservers WHERE IP = p_IP);
        SET v_podid = (SELECT podid FROM appservers WHERE IP = p_IP);
        
        -- Insert into tomcats table
        INSERT INTO tomcats (newname, tomcatlocation, portno, appid, used, emversionid, podid, active, updatedby)
        VALUES (p_newname, '/eClinicalWorks/prodapps/', p_portno, v_appid, 1, 10, v_podid, 1, p_instname);
        
        IF tomcatrename IS NULL THEN
            -- Fetch application ID and Tomcat ID
            SET v_appid = (SELECT appid FROM appservers WHERE IP = p_IP);
            SET v_tid = (SELECT tid FROM tomcats WHERE newname = p_newname);
            
            -- Insert into renameaction table
            INSERT INTO renameaction (IP, tid, appid, vid, newname, complete, checked, instname, instemail, dbname, dbuname, dbpass)
            VALUES (p_IP, v_tid, v_appid, 10, p_newname, 1, 1, p_instname, p_instemail, p_dbname, p_dbuname, p_dbpass);
        ELSE
            -- Log a message indicating that the tomcat is already present in the renameaction table
            SET @message = 'Tomcat is already present in renameaction TABLE';
        END IF;
    ELSE
        -- Log a message indicating that the tomcat is already present in the tomcat table
        SET @message = 'Tomcat is already present in tomcat table';
    END IF;   
END //

DELIMITER ;

-- End of the SQL Script
