update mysql_argus_config set ip='10.50.3.245',paas_hostname='ecw50-mysql80-prod08-portal078-b4e7-022kb47a4cd' where id=875 and ip='10.50.3.235' limit 1; 

DELIMITER $$

CREATE PROCEDURE UpdateMySQLArgusConfig(
    IN p_new_ip VARCHAR(15),
    IN p_paas_hostname VARCHAR(255),
    IN p_id INT,
    IN p_old_ip VARCHAR(15)
)
BEGIN
    -- Declare a variable to store the number of affected rows
    DECLARE affected_rows INT DEFAULT 0;
    
    -- Validate inputs
    IF p_new_ip IS NULL OR p_paas_hostname IS NULL OR p_id IS NULL OR p_old_ip IS NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Input parameters cannot be NULL';
    ELSE
        -- Execute the update query
        UPDATE mysql_argus_config 
        SET ip = p_new_ip, paas_hostname = p_paas_hostname
        WHERE id = p_id AND ip = p_old_ip
        LIMIT 1;

        -- Check how many rows were affected
        SET affected_rows = ROW_COUNT();

        -- If no rows were affected, raise an error
        IF affected_rows = 0 THEN
            SIGNAL SQLSTATE '45000'
            SET MESSAGE_TEXT = 'No rows updated. Either the id or old ip does not match.';
        END IF;
    END IF;
    
END$$

DELIMITER ;
