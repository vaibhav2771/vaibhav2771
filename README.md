
  

    DELIMITER //

CREATE PROCEDURE rename_tomcat (
    IN p_newname VARCHAR(255),
    IN p_appid INT,
    IN p_podid INT,
    IN p_executed_by VARCHAR(255),
    IN p_IRNUMBER VARCHAR(20)
)
BEGIN
    DECLARE v_tomcat_count INT;
    DECLARE v_renameaction_count INT;
    DECLARE v_old_name VARCHAR(255);
    DECLARE v_message VARCHAR(100);

    -- Check if the new name already exists in tomcats
    SELECT COUNT(*) INTO v_tomcat_count FROM tomcats WHERE newname = p_newname AND appid = p_appid;

    -- Check if the new name already exists in renameaction
    SELECT COUNT(*) INTO v_renameaction_count FROM renameaction WHERE newname = p_newname AND appid = p_appid;

    IF v_tomcat_count > 0 THEN
        SET v_message = 'New name already exists in tomcats.';
    ELSEIF v_renameaction_count > 0 THEN
        SET v_message = 'New name already exists in renameaction.';
    ELSE
        -- Select the old Tomcat name
        SELECT newname INTO v_old_name FROM tomcats WHERE appid = p_appid AND podid = p_podid;

        -- Update tomcats table
        UPDATE tomcats SET newname = p_newname WHERE newname = v_old_name AND appid = p_appid AND podid = p_podid;

        -- Update renameaction table (if needed)
        UPDATE renameaction SET newname = p_newname WHERE newname = v_old_name AND appid = p_appid;

        SET v_message = 'Tomcat name renamed successfully from "' || v_old_name || '" to "' || p_newname || '"';
    END IF;

    -- Log the stored procedure execution
    INSERT INTO storedproc_logs (sp_name, executed_by, executed_on, message, IR_Number)
    VALUES ('rename_tomcat', p_executed_by, NOW(), v_message, p_IRNUMBER);
END //

DELIMITER ;
