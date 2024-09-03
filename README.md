
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
        -- Update tomcats table (replace with actual update logic)
        UPDATE tomcats SET ... WHERE ...;

        SET v_message = 'Tomcat renamed successfully.';
    END IF;

    -- Insert into renameaction table (if needed)
    INSERT INTO renameaction (newname, appid, podid, executed_by, IRNUMBER)
    VALUES (p_newname, p_appid, p_podid, p_executed_by, p_IRNUMBER);
END //

DELIMITER ;





DELIMITER //

CREATE PROCEDURE disable_tomcat3 (
    IN p_newname VARCHAR(255),
    IN p_appid VARCHAR(255), -- Assuming appid is a string containing multiple values
    IN p_podid INT,
    IN p_portno INT,
    IN p_executed_by VARCHAR(255),
    IN p_IRNUMBER VARCHAR(20)
)
BEGIN
    DECLARE p_message VARCHAR(255);
    DECLARE v_limit INT;

    -- Split appid into a list if it's a string
    SET p_appid = REPLACE(p_appid, ',', ','); -- Replace ',' with ', ' to handle multiple commas
    SET @appid_list = CONCAT('(', p_appid, ')');

    -- Get the number of records for the specified podid and appid list
    SELECT COUNT(*) INTO v_limit FROM tomcats WHERE podid = p_podid AND appid IN @appid_list;

    -- Update the data with the dynamic limit (or without limit if needed)
    UPDATE tomcats SET ... WHERE podid = p_podid AND appid IN @appid_list LIMIT v_limit;

    -- ... rest of the procedure ...
END //

DELIMITER ;


DELIMITER //

CREATE PROCEDURE update_ebo_data (
    IN p_eboversion INT,
    IN p_podid INT,
    IN p_practicefolder VARCHAR(255)
)
BEGIN
    DECLARE v_limit INT;

    -- Get the number of records for the specified practice folder
    SELECT COUNT(*) INTO v_limit FROM ebo WHERE practicefolder = p_practicefolder;

    -- Update the data with the dynamic limit
    IF v_limit > 0 THEN
        UPDATE ebo
        SET eboversion = p_eboversion
        WHERE podid IN (p_podid)
        AND eboversion != p_eboversion
        AND practicefolder = p_practicefolder
        LIMIT v_limit;
    ELSE
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'No records found for the specified practice folder.';
    END IF;
END //

DELIMITER ;
