
DELIMITER $$

CREATE PROCEDURE UpdateCcmrTomcatsDynamic(
    IN p_TABLENAME VARCHAR(255),
    IN p_COMPONENT VARCHAR(255),
    IN p_subcomponent VARCHAR(255),
    IN p_tomcatname VARCHAR(255),
    IN p_podid INT,
    IN p_COLUMNNAME VARCHAR(255),
    IN p_ccmrapids VARCHAR(255),
    IN p_executed_by VARCHAR(255) -- New parameter for logging purposes
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        -- Log the error in the Storedproc_logs table
        INSERT INTO Storedproc_logs(sp_name, executed_by, executed_on, message)
        VALUES('UpdateCcmrTomcatsDynamic', p_executed_by, NOW(), 'Error occurred during execution');
    END;
    
    -- Validation: check if inputs are valid
    IF p_TABLENAME IS NULL OR p_COMPONENT IS NULL OR p_subcomponent IS NULL OR p_tomcatname IS NULL OR p_podid IS NULL OR p_COLUMNNAME IS NULL OR p_ccmrapids IS NULL THEN
        -- Log the validation error
        INSERT INTO Storedproc_logs(sp_name, executed_by, executed_on, message)
        VALUES('UpdateCcmrTomcatsDynamic', p_executed_by, NOW(), 'Invalid input parameters');
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Invalid input parameters';
    ELSE
        -- Construct dynamic query
        SET @query = CONCAT(
            'UPDATE ', p_TABLENAME, ' SET COMPONENT = ? , subcomponent = ? ',
            'WHERE tomcatname = ? AND podid = ? AND ', p_COLUMNNAME, ' IN (', p_ccmrapids, ')'
        );

        -- Prepare and execute the statement
        PREPARE stmt FROM @query;
        EXECUTE stmt USING p_COMPONENT, p_subcomponent, p_tomcatname, p_podid;
        DEALLOCATE PREPARE stmt;

        -- Log success in the Storedproc_logs table
        INSERT INTO Storedproc_logs(sp_name, executed_by, executed_on, message)
        VALUES('UpdateCcmrTomcatsDynamic', p_executed_by, NOW(), 'Stored procedure executed successfully');
    END IF;
END $$

DELIMITER ;
