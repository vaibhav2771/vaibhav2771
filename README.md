

DELIMITER //

CREATE PROCEDURE UpdateTomcatPort(
    IN p_portno VARCHAR(10),
    IN p_newname VARCHAR(100),
    IN p_executed_by VARCHAR(255),
    IN p_tid INT,
    IN p_ir_number VARCHAR(20)
)
BEGIN
    DECLARE p_message VARCHAR(255);

    -- Check if the entry of newname and tid exists or not
    IF EXISTS (SELECT 1 FROM tomcats WHERE newname = p_newname AND tid = p_tid) THEN
        -- Update the entry if it exists
        UPDATE tomcats SET portno = p_portno WHERE newname = p_newname AND tid = p_tid LIMIT 1;
        SET p_message = 'Records updated successfully.';
    ELSE
        SET p_message = 'No records were updated. Please check whether you have put correct value of tid';
    END IF;

    INSERT INTO storedproc_logs (sp_name, executed_by, executed_on, Message, IR_Number)
    VALUES ('UpdateTomcatPort', p_executed_by, NOW(), p_message, p_ir_number);
END //

DELIMITER ;








   
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
