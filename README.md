
DELIMITER //

CREATE PROCEDURE UpdateTomcatConfig(
    IN p_portno VARCHAR(10),
    IN p_newname VARCHAR(100),
    IN p_tid INT
)
BEGIN
    -- Check if the entry exists
    IF EXISTS (SELECT 1 FROM tomcats WHERE newname = p_newname AND tid = p_tid) THEN
        -- Update the entry if it exists
        UPDATE tomcats 
        SET portno = p_portno 
        WHERE newname = p_newname AND tid = p_tid 
        LIMIT 1;
    ELSE
        -- If no entry exists, optionally you can handle the case here, e.g., insert or log
        SELECT 'No matching entry found to update';
    END IF;
END //

DELIMITER ;
